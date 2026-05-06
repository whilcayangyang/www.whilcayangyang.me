---
title: "On-Prem Kubernetes Homelab"
description: "GitOps-driven Talos Linux cluster: Flux reconciles all state from Git, with sealed secrets, wildcard TLS, MetalLB load balancing, and a full Prometheus/Loki/Grafana observability stack."
weight: 40
showTableOfContents: true
draft: false
---

## Talos Linux On-Prem — GitOps, Production Discipline

{{< lead >}}
A single-node Talos Linux cluster run as a GitOps platform: Flux continuously reconciles cluster state from Git, and no manual `kubectl apply` ever touches production. Sealed secrets, wildcard TLS automation, MetalLB load balancing, middleware-enforced security headers, and a unified Prometheus/Loki/Grafana observability stack complete the picture.
{{< /lead >}}

{{< keywordList >}}
{{< keyword icon="code" >}} GitOps — Flux CD {{< /keyword >}}
{{< keyword icon="server" >}} Talos Linux / Kubernetes {{< /keyword >}}
{{< keyword icon="shield" >}} Sealed Secrets + TLS {{< /keyword >}}
{{< keyword icon="globe" >}} Traefik Ingress {{< /keyword >}}
{{< keyword icon="eye" >}} Prometheus + Loki {{< /keyword >}}
{{< keyword icon="cloud" >}} Cloudflare Tunnel {{< /keyword >}}
{{< keyword icon="lock" >}} VolSync Backup {{< /keyword >}}
{{< /keywordList >}}

{{< figure src="dashboard.png" alt="Homelab dashboard showing infrastructure, networking, and application services" >}}

{{< figure src="k9s.png" alt="k9s terminal UI showing Kubernetes pods across all namespaces" >}}

<div class="mt-4">
{{< alert icon="circle-info" >}}
**Design Principle:** The Git repository is the single source of truth. Flux enforces it. No manual cluster changes — if it isn't in Git, it doesn't exist in the cluster.
{{< /alert >}}
</div>

---

## Architecture Overview

The cluster runs on bare-metal on-prem hardware provisioned declaratively with **Talos Linux** — an immutable, API-driven OS purpose-built for Kubernetes. Node configuration is fully codified and applied via a task runner; there is no SSH access, no shell login, and no manual OS-level state. Flux (`flux-system`) watches the Git repository and reconciles every manifest, Helm release, and Kustomization automatically. All service exposure is handled by Traefik as the single ingress point, with MetalLB assigning external IPs for `LoadBalancer` services. Cloudflare Tunnel (`cloudflared`) provides public reachability without opening inbound firewall ports.

| Layer | Component | Namespace |
|---|---|---|
| **GitOps** | **Flux CD** | **`flux-system`** |
| Secret encryption | sealed-secrets | `kube-system` |
| TLS issuance | cert-manager | `cert-manager` |
| TLS distribution | reflector | `kube-system` |
| Ingress | Traefik | `traefik` |
| Load balancer | MetalLB | `metallb-system` |
| Home dashboard | Homepage | `homepage` |
| DNS sink | AdGuard Home | `adguard` |
| Media server | Jellyfin | `jellyfin` |
| File sharing | Pairdrop | `pairdrop` |
| Password manager | Vaultwarden + PostgreSQL | `vaultwarden` |
| Dev environment | code-server | `code-server` |
| Infra dashboard | Portainer | `portainer` |
| Static site | Caddy | `caddy` |
| Public tunnel | cloudflared | `cloudflared` |
| Metrics | Prometheus | `monitoring` |
| Dashboards | Grafana | `monitoring` |
| Log aggregation | Loki (single-binary) | `monitoring` |
| Log collection | Alloy (DaemonSet) | `monitoring` |
| Alerting | AlertManager | `monitoring` |
| Security scanning | Trivy Operator | `trivy-system` |
| Backup/restore | VolSync | `volsync-system` |

---

## Platform Breakdown

{{< tabs group="homelab-stack" >}}

{{< tab label="GitOps" icon="code" >}}

**Flux CD — Continuous Reconciliation**

[Flux](https://fluxcd.io/) runs in the `flux-system` namespace and is the operational core of the cluster. It watches the Git repository for changes and continuously reconciles the actual cluster state against the declared state. Every manifest, Helm release, and Kustomization layer is managed through Flux — not applied manually.

The reconciliation loop means configuration drift is impossible to sustain: any manual `kubectl apply` or in-cluster edit is overwritten on the next sync cycle.

Flux components in use:

| Controller | Role |
|---|---|
| `source-controller` | Pulls from Git and Helm repositories, produces versioned artifacts |
| `kustomize-controller` | Applies Kustomization stacks in dependency order |
| `helm-controller` | Manages `HelmRelease` CRDs — upgrades, rollbacks, values reconciliation |
| `notification-controller` | Emits events on reconciliation success/failure |

**Repository Layout**

All cluster configuration is structured so Flux's Kustomize controller can resolve dependencies in the correct order — CRDs before controllers, controllers before workloads. `SealedSecret` manifests are committed alongside their consuming `Deployment`s; plaintext secrets never appear in the repository.

{{< /tab >}}

{{< tab label="Infrastructure" icon="server" >}}

**Node Provisioning — Talos Linux**

Talos Linux is an immutable, minimal OS with no shell, no SSH, and no package manager — all configuration is applied through a declarative machine config over a secured API. Node provisioning is fully automated via a task runner that codifies every step: generating machine configs, applying patches, and bootstrapping the cluster. Key configuration concerns (network settings, kernel parameters, kubelet flags, and cluster extras) are expressed as structured patches rather than imperative commands.

This model means the OS layer is as auditable and reproducible as the Kubernetes layer above it — any node can be re-provisioned from scratch without manual intervention.

**Secret Management**

[Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) runs in `kube-system` and handles encryption of all cluster secrets. Raw Kubernetes `Secret` manifests are never committed to Git — only `SealedSecret` CRDs encrypted with the controller's public key. This makes the GitOps repository safe to store in version control without exposing credentials.

**TLS — cert-manager**

cert-manager issues a single wildcard certificate via **Cloudflare DNS-01** challenge. The resulting secret is automatically mirrored by **reflector** into every service namespace declared in the Certificate's annotations.

All `IngressRoute` and `Ingress` resources reference the same TLS secret — no manual secret copying, no per-namespace certificate requests.

**Ingress — Traefik**

Traefik runs in the `traefik` namespace and is the single ingress controller for all services. Middleware definitions (security headers, rate limiting, IP allowlisting) are declared in a `ConfigMap` mounted as a file provider inside the Traefik pod and referenced in annotations.

**Load Balancer — MetalLB**

MetalLB runs in the `metallb-system` namespace and assigns external IPs to `LoadBalancer`-type services. This enables services like AdGuard Home to receive a stable, LAN-reachable IP without relying on NodePort or host networking.

**Public Tunnel — cloudflared**

`cloudflared` in the `cloudflared` namespace creates an outbound-only Cloudflare Tunnel with two replicas for high availability. Public services route through this tunnel — no inbound firewall rules required, no exposed NodePorts. Internal-only services remain behind the IP allowlist middleware and are never reachable externally.

**PVC Backup — VolSync**

[VolSync](https://volsync.readthedocs.io/) runs in the `volsync-system` namespace and handles asynchronous replication of PersistentVolumeClaim data off-cluster. Each stateful workload declares a `ReplicationSource` CRD that schedules periodic snapshots and pushes them to an external destination. A corresponding `ReplicationDestination` CRD allows point-in-time restore by pulling a named snapshot back into a fresh PVC.

{{< /tab >}}

{{< tab label="Services" icon="cog" >}}

**DNS — AdGuard Home**

AdGuard Home (`adguard` namespace) serves as the local DNS resolver and ad/tracker sink for the LAN. It receives a dedicated external IP via MetalLB and listens on port 53, making it the network-wide DNS server. Upstream resolvers are configured for encrypted DNS-over-HTTPS.

**Media — Jellyfin**

Jellyfin (`jellyfin` namespace) is the self-hosted media server. Accessible internally via Traefik IngressRoute with the wildcard TLS cert.

**File Sharing — Pairdrop**

Pairdrop (`pairdrop` namespace) provides local wireless file transfers — a self-hosted alternative to AirDrop that works across platforms on the same network.

**Password Manager — Vaultwarden + PostgreSQL**

Vaultwarden runs in the `vaultwarden` namespace backed by a PostgreSQL instance in the same namespace. Provides a self-hosted Bitwarden-compatible password manager. Data is persistent via a PersistentVolumeClaim; PostgreSQL credentials are managed through sealed-secrets. SMTP is handled via an external mail relay.

**Dev Environment — code-server**

code-server (`code-server` namespace) exposes VS Code as a web application. Uses a relaxed CSP/frame policy via a dedicated Traefik middleware to allow the VS Code web UI to function correctly.

**Home Dashboard — Homepage**

Homepage (`homepage` namespace) serves as the service launcher — a configurable start page with widgets for each self-hosted service and a live Kubernetes cluster widget showing pod/node status.

**Infra Dashboard — Portainer**

Portainer (`portainer` namespace) provides a visual interface for cluster and container lifecycle management, running in Kubernetes mode.

**Static Site — Caddy**

Caddy (`caddy` namespace) serves the static site using raw manifests only, with content synced from a local build. Sits behind Traefik for TLS termination and routing.

{{< /tab >}}

{{< tab label="Security" icon="shield" >}}

**Security Scanning — Trivy Operator**

Trivy Operator runs in the `trivy-system` namespace and provides continuous in-cluster scanning across four domains:

| Scan type | What it covers |
|---|---|
| Vulnerability | Container image CVEs against upstream advisory DBs |
| Config audit | Kubernetes manifest misconfigurations (e.g. privileged containers, missing resource limits) |
| RBAC assessment | Overly permissive roles and bindings across namespaces |
| Secret scanning | Hardcoded credentials and tokens in workload specs |

Results are surfaced as Kubernetes CRDs (`VulnerabilityReport`, `ConfigAuditReport`, `RbacAssessmentReport`, `ExposedSecretReport`) and exposed as Prometheus metrics — scraped by the existing Prometheus instance in `monitoring` and visible in Grafana.

**Traefik Middleware Chain**

Middlewares are defined in a `ConfigMap` mounted as a file provider inside the Traefik pod and referenced in `IngressRoute` annotations.

| Middleware | Purpose |
|---|---|
| `secure-headers` | HSTS, frameDeny, nosniff, referrer-policy |
| `code-server-headers` | Relaxed CSP/frame policy for VS Code web UI |
| `rate-limit` | Request rate limiting with burst tolerance |
| `ip-allowlist` | LAN + cluster CIDR only |

All internal services apply the IP allowlist — requests from outside the LAN or cluster CIDR are rejected at the ingress layer before reaching any application. Public services exposed through `cloudflared` bypass the allowlist via a dedicated IngressRoute entry.

**Secret Lifecycle**

All secrets follow this flow:
1. Generate or retrieve credential
2. Encrypt with `kubeseal` using the controller's public key
3. Commit `SealedSecret` manifest to Git
4. Flux detects the commit and syncs; controller decrypts and creates the `Secret` in-cluster

No plaintext secrets in Git. No manual `kubectl create secret` commands. Every secret change has a Git commit as its audit trail.

**TLS Flow**

```
cert-manager (DNS-01 via Cloudflare API)
  └─ issues: wildcard TLS secret (in cert-manager ns)
       └─ reflector mirrors → all service namespaces
            └─ IngressRoute references mirrored TLS secret
```

cert-manager handles automatic renewal. reflector handles propagation. Services reference the secret by name — zero manual intervention on cert rotation.

{{< /tab >}}

{{< tab label="Observability" icon="eye" >}}

**Metrics — Prometheus**

Prometheus (`monitoring` namespace) scrapes metrics from all cluster workloads and the underlying nodes. Configured with multiple replicas and extended retention. Service monitors are declared as `ServiceMonitor` CRDs co-located with their target deployments. Trivy Operator security scan results are also exposed as Prometheus metrics, making security posture visible alongside infrastructure health.

**Dashboards — Grafana**

Grafana (`monitoring` namespace) provides dashboards for both metrics (Prometheus datasource) and logs (Loki datasource pre-configured). Accessible internally via Traefik.

**Log Aggregation — Loki**

Loki runs in single-binary mode in the `monitoring` namespace — appropriate for single-node homelab scale without the operational overhead of microservices mode. Configured with short retention suitable for local-path storage.

**Log Collection — Alloy**

Grafana Alloy runs as a `DaemonSet` in the `monitoring` namespace, collecting logs from all pods across the cluster and forwarding them to Loki. Alloy replaces the deprecated promtail. Configuration is declared as a `ConfigMap` and managed in Git.

**Alerting — AlertManager**

AlertManager (`monitoring` namespace) handles alert routing from Prometheus with multiple replicas for reliability. Alert rules are defined as `PrometheusRule` CRDs and version-controlled alongside the rest of the cluster configuration. Routing is configured for Slack with separate channels for critical and warning severity.

The full observability pipeline: **Alloy → Loki** for logs, **Prometheus → AlertManager** for metric-based alerts, **Grafana** for unified visibility.

{{< /tab >}}

{{< /tabs >}}

---

## Lessons Applied

These principles emerged from running this cluster under real conditions:

1. **GitOps is the only sane operational model** — Flux makes drift impossible and every change auditable. Without it, cluster state diverges from documentation faster than documentation gets updated.
2. **Immutable OS, immutable cluster** — Talos Linux eliminates an entire category of undocumented state. There is no shell to log into and make a one-off change that never makes it back to Git.
3. **Namespace isolation is not optional** — one misconfigured deployment should not be able to reach secrets in another namespace.
4. **Automate TLS end-to-end or suffer cert rot** — cert-manager + reflector eliminates an entire class of silent failures.
5. **Seal secrets before they touch Git** — retrofitting secret hygiene is painful and leaves audit trail gaps.
6. **Single ingress controller, one middleware source of truth** — proliferating ingress patterns create inconsistent security postures.
7. **Build the observability stack first** — deploying Prometheus and Loki before services means every deployment is observable from day one.
8. **File providers for Traefik middleware** — avoids CRD sprawl and keeps middleware definitions reviewable in a single ConfigMap.
9. **MetalLB unlocks clean service exposure** — assigning stable external IPs to LoadBalancer services (especially DNS) avoids NodePort hacks and keeps routing predictable.

---

## Closing Thoughts

This cluster is a GitOps-first engineering platform built on Talos Linux. The OS is declarative. Flux is the enforcer: the Git repository is the cluster. Every secret is sealed, every service terminates TLS from the same wildcard cert, and every log line flows to Loki.

The discipline isn't complexity for its own sake — it's what makes a single-node homelab operationally honest: no undocumented state, no forgotten manual changes, no certificates expiring unnoticed. **If it isn't in Git, it doesn't run.**
