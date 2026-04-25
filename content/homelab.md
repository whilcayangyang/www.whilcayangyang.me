---
title: "On-Prem Kubernetes Homelab"
description: "Production-grade k3s cluster running self-hosted services with Traefik, cert-manager, sealed-secrets, and a full observability stack."
weight: 40
showTableOfContents: true
draft: false
---

## K3s On-Prem — Single-Node, Production Discipline

{{< lead >}}
A single-node k3s cluster engineered with the same operational rigour as production: sealed secrets, wildcard TLS automation, middleware-enforced security headers, and a unified Prometheus/Loki/Grafana observability stack.
{{< /lead >}}

{{< keywordList >}}
{{< keyword icon="server" >}} k3s / Kubernetes {{< /keyword >}}
{{< keyword icon="shield" >}} Sealed Secrets + TLS {{< /keyword >}}
{{< keyword icon="globe" >}} Traefik Ingress {{< /keyword >}}
{{< keyword icon="eye" >}} Prometheus + Loki {{< /keyword >}}
{{< keyword icon="cloud" >}} Cloudflare Tunnel {{< /keyword >}}
{{< keyword icon="code" >}} GitOps {{< /keyword >}}
{{< /keywordList >}}

{{< figure src="dashboard.png" alt="Homelab dashboard showing infrastructure, networking, and application services" >}}

<div class="mt-4">
{{< alert icon="circle-info" >}}
**Design Principle:** Every component runs in a dedicated namespace with least-privilege access. Infrastructure state lives in Git — no manual cluster changes.
{{< /alert >}}
</div>

---

## Architecture Overview

The cluster runs on bare-metal on-prem hardware with k3s as the Kubernetes distribution. All service exposure is handled by Traefik as the single ingress point. Cloudflare Tunnel (`cloudflared`) provides public reachability without opening inbound firewall ports.

| Layer | Component | Namespace |
|---|---|---|
| Secret encryption | sealed-secrets | `kube-system` |
| TLS issuance | cert-manager | `cert-manager` |
| TLS distribution | reflector | `kube-system` |
| Ingress | Traefik | `traefik` |
| DNS sink | AdGuard Home | `adguard` |
| Media server | Jellyfin | `jellyfin` |
| File sharing | Pairdrop | `pairdrop` |
| Password manager | Vaultwarden + PostgreSQL | `vaultwarden` |
| Dev environment | code-server | `code-server` |
| Home dashboard | Homepage | `homepage` |
| Infra dashboard | Portainer | `portainer` |
| Static site | Caddy | `caddy` |
| Public tunnel | cloudflared | `cloudflared` |
| Metrics | Prometheus | `monitoring` |
| Dashboards | Grafana | `monitoring` |
| Log aggregation | Loki (single-binary) | `monitoring` |
| Log collection | Alloy (DaemonSet) | `monitoring` |
| Alerting | AlertManager | `monitoring` |

---

## Platform Breakdown

{{< tabs group="homelab-stack" >}}

{{< tab label="Infrastructure" icon="server" >}}

**Secret Management**

[Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) runs in `kube-system` and handles encryption of all cluster secrets. Raw Kubernetes `Secret` manifests are never committed to Git — only `SealedSecret` CRDs encrypted with the controller's public key. This makes the GitOps repository safe to store in version control without exposing credentials.

**TLS — cert-manager**

cert-manager issues a single wildcard certificate via **Cloudflare DNS-01** challenge. The resulting secret is automatically mirrored by **reflector** into every service namespace declared in the Certificate's `secretTemplate` annotations.

All `IngressRoute` and `Ingress` resources reference the same TLS secret — no manual secret copying, no per-namespace certificate requests.

**Ingress — Traefik**

Traefik runs in the `traefik` namespace and is the single ingress controller for all services. Middleware definitions (security headers, rate limiting, IP allowlisting) are declared in a `ConfigMap` mounted as a file provider inside the Traefik pod and referenced in annotations as `<name>@file`.

**Public Tunnel — cloudflared**

`cloudflared` in the `cloudflared` namespace creates an outbound-only Cloudflare Tunnel. Public services route through this tunnel — no inbound firewall rules required, no exposed NodePorts. Internal-only services remain behind the IP allowlist middleware and are never reachable externally.

{{< /tab >}}

{{< tab label="Services" icon="cog" >}}

**DNS — AdGuard Home**

AdGuard Home (`adguard` namespace) serves as the local DNS resolver and ad/tracker sink for the LAN. Upstream resolvers are configured for encrypted DNS-over-HTTPS.

**Media — Jellyfin**

Jellyfin (`jellyfin` namespace) is the self-hosted media server. Accessible internally via Traefik IngressRoute with the wildcard TLS cert.

**File Sharing — Pairdrop**

Pairdrop (`pairdrop` namespace) provides local wireless file transfers — a self-hosted alternative to AirDrop that works across platforms on the same network.

**Password Manager — Vaultwarden + PostgreSQL**

Vaultwarden runs in the `vaultwarden` namespace backed by a PostgreSQL instance in the same namespace. Provides a self-hosted Bitwarden-compatible password manager. Data is persistent via a PersistentVolumeClaim; PostgreSQL credentials are managed through sealed-secrets.

**Dev Environment — code-server**

code-server (`code-server` namespace) exposes VS Code as a web application. Uses a relaxed CSP/frame policy via a dedicated Traefik middleware (`code-server-headers@file`) to allow the VS Code web UI to function correctly.

**Home Dashboard — Homepage**

Homepage (`homepage` namespace) serves as the service launcher — a configurable start page with widgets for each self-hosted service and a live Kubernetes cluster widget showing pod/node status.

**Infra Dashboard — Portainer**

Portainer (`portainer` namespace) provides a visual interface for cluster and container lifecycle management.

**Static Site — Caddy**

Caddy (`caddy` namespace) serves the static site. Sits behind Traefik for TLS termination and routing.

{{< /tab >}}

{{< tab label="Security" icon="shield" >}}

**Traefik Middleware Chain**

Middlewares are defined in a `ConfigMap` mounted as a file provider inside the Traefik pod and referenced in `IngressRoute` annotations as `<name>@file`.

| Middleware | Purpose |
|---|---|
| `secure-headers` | HSTS, frameDeny, nosniff, referrer-policy |
| `code-server-headers` | Relaxed CSP/frame policy for VS Code web UI |
| `rate-limit` | 100 req/s average, burst 50 |
| `ip-allowlist` | LAN + cluster CIDR only |

All internal services apply `ip-allowlist@file` — requests from outside the LAN or cluster CIDR are rejected at the ingress layer before reaching any application. Public services exposed through `cloudflared` bypass the allowlist via a dedicated IngressRoute entry.

**Secret Lifecycle**

All secrets follow this flow:
1. Generate or retrieve credential
2. Encrypt with `kubeseal` using the controller's public key
3. Commit `SealedSecret` manifest to Git
4. Controller decrypts and creates the `Secret` in-cluster

No plaintext secrets in Git. No manual `kubectl create secret` commands.

**TLS Flow**

```
cert-manager (DNS-01 via Cloudflare API)
  └─ issues: wildcard TLS secret (in cert-manager ns)
       └─ reflector mirrors → jellyfin, vaultwarden, code-server, ...
            └─ IngressRoute references mirrored TLS secret
```

cert-manager handles automatic renewal. reflector handles propagation. Services reference the secret by name — zero manual intervention on cert rotation.

{{< /tab >}}

{{< tab label="Observability" icon="eye" >}}

**Metrics — Prometheus**

Prometheus (`monitoring` namespace) scrapes metrics from all cluster workloads and the underlying nodes. Service monitors are declared as `ServiceMonitor` CRDs co-located with their target deployments.

**Dashboards — Grafana**

Grafana (`monitoring` namespace) provides dashboards for both metrics (Prometheus datasource) and logs (Loki datasource). Accessible internally via Traefik.

**Log Aggregation — Loki**

Loki runs in single-binary mode in the `monitoring` namespace — appropriate for single-node homelab scale without the operational overhead of microservices mode.

**Log Collection — Alloy**

Grafana Alloy runs as a `DaemonSet` in the `monitoring` namespace, collecting logs from all pods across the cluster and forwarding them to Loki. Configuration is declared as a `ConfigMap` and managed in Git.

**Alerting — AlertManager**

AlertManager (`monitoring` namespace) handles alert routing from Prometheus. Alert rules are defined as `PrometheusRule` CRDs and version-controlled alongside the rest of the cluster configuration.

The full observability pipeline: **Alloy → Loki** for logs, **Prometheus → AlertManager** for metric-based alerts, **Grafana** for unified visibility.

{{< /tab >}}

{{< /tabs >}}

---

## Lessons Applied

These principles emerged from running this cluster under real conditions:

1. **Namespace isolation is not optional** — one misconfigured deployment should not be able to reach secrets in another namespace.
2. **Automate TLS end-to-end or suffer cert rot** — cert-manager + reflector eliminates an entire class of silent failures.
3. **Seal secrets before they touch Git** — retrofitting secret hygiene is painful and leaves audit trail gaps.
4. **Single ingress controller, one middleware source of truth** — proliferating ingress patterns create inconsistent security postures.
5. **Build the observability stack first** — deploying Prometheus and Loki before services means every deployment is observable from day one.
6. **File providers for Traefik middleware** — avoids CRD sprawl and keeps middleware definitions reviewable in a single ConfigMap.

---

## Closing Thoughts

This cluster is a continuously operated engineering platform — not a set-and-forget lab. Every component is managed through Git, every secret is sealed, every service terminates TLS from the same wildcard cert, and every log line flows to Loki.

The objective is not complexity. The objective is **operational clarity under real conditions**.
