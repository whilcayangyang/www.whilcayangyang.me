---
title: "Building an Enterprise-Grade Hybrid Homelab"
description: "Cloud + on-prem architecture with AWS, Cloudflare, Linux, WireGuard, and infrastructure automation."
weight: 40
showTableOfContents: true
draft: false
---

## Cloud + On-Prem Architecture with AWS, Cloudflare, and Linux

{{< lead >}}
Modern infrastructure should be portable, observable, secure, and automated. This homelab is built and operated as a production-grade hybrid environment — a living platform for continuous infrastructure engineering practice.
{{< /lead >}}

{{< keywordList >}}
{{< keyword icon="cloud" >}} AWS + Cloudflare {{< /keyword >}}
{{< keyword icon="globe" >}} Hybrid Networking {{< /keyword >}}
{{< keyword icon="shield" >}} Zero-Trust Security {{< /keyword >}}
{{< keyword icon="code" >}} Infrastructure as Code {{< /keyword >}}
{{< keyword icon="eye" >}} Observability {{< /keyword >}}
{{< keyword icon="cog" >}} Automation {{< /keyword >}}
{{< /keywordList >}}

<div class="mt-4">
{{< alert icon="circle-info" >}}
**Design Principle:** Treat the homelab like production. Every component is version-controlled, automated where possible, and secured by default.
{{< /alert >}}
</div>

---

## Architecture Overview

This environment is structured as a unified hybrid platform — not a loose collection of services, but a deliberately engineered system where every layer has a clear role.

- **Cloud Layer:** AWS + Cloudflare for internet-facing services and edge enforcement
- **On-Prem Layer:** Beelink server + MikroTik router for local workloads and private services
- **Secure Interconnect:** WireGuard encrypted tunnels bridging home and cloud
- **Platform Model:** Docker-based workloads managed and monitored continuously
- **Infrastructure Delivery:** Terraform + GitHub for reproducible, auditable provisioning

All infrastructure changes flow through GitHub, making every deployment deterministic and traceable.

---

## Platform Breakdown

{{< tabs group="homelab-stack" >}}

{{< tab label="Cloud Layer" icon="cloud" >}}

**AWS Infrastructure**

All AWS and Cloudflare resources are provisioned through **Terraform** and version-controlled in **GitHub** — no manual console changes, no configuration drift.

Benefits of this approach:
- Deterministic deployments across rebuilds
- Full infrastructure traceability through git history
- Rapid recovery capability from a clean state
- Consistent environments that behave the same every time

**EC2 Baseline**

- Hardened EC2 hosts running Debian 13
- Docker runtime for containerized workloads
- WireGuard peer maintaining the encrypted tunnel to on-prem

Debian is chosen for long-term stability, minimal overhead, and predictable release behaviour.

**Container Stack**

- **Traefik** — reverse proxy and dynamic routing
- **Caddy** — web serving
- **Gatus** — availability monitoring and uptime checks
- **Beszel** — host and service metrics
- **Duin** — automated container image upgrades
- **Pi-hole** — DNS-level filtering and ad blocking
- **Portainer** — container lifecycle management
- **Vaultwarden** — self-hosted password manager

**Core AWS Services**

- VPC, Security Groups, and NACLs for network segmentation and access control
- S3 for object storage
- SES for transactional email notifications
- IAM with least-privilege role-based access
- SSM for secure remote administration — no exposed SSH ports

**Cloudflare Edge**

- Domain and DNS management with full zone control
- WAF for Layer 7 threat protection
- CDN for low-latency global delivery
- Workers for lightweight edge logic and routing

Cloudflare adds DDoS mitigation, TLS termination, and intelligent edge enforcement in front of all public-facing services.

{{< /tab >}}

{{< tab label="On-Prem Layer" icon="server" >}}

**Local Infrastructure**

Primary node: **Beelink SER8** running Debian 13 with Docker runtime.

Primary roles:
- Media and entertainment services
- Local automation hub and scheduling
- Secondary service environment for private workloads
- Hybrid cloud peer via WireGuard

**On-Prem Container Services**

Core infrastructure:
- Traefik (local routing and reverse proxy)
- Beszel agent (metrics forwarded to cloud dashboard)
- Duin (automated container upgrades)
- Portainer (container lifecycle management)

Streaming stack:
- **Jellyfin** — self-hosted media server
- **Bazarr** — subtitle management
- **Prowlarr** — indexer aggregation
- **Radarr** — media library automation
- **qBittorrent** — download client
- **Gluetun** — VPN container for network isolation
- **PairDrop** — local wireless file sharing

The streaming pipeline is routed behind Gluetun, isolating download traffic from the trusted local network and enforcing separate traffic paths.

{{< /tab >}}

{{< tab label="Network & Security" icon="shield" >}}

**Network Core**

The **MikroTik hAP ax3** handles all routing, segmentation, and access control responsibilities:

- Routing and VLAN-based segmentation
- Stateful firewall policy enforcement
- DHCP services across network segments
- Secure Wi-Fi using WPA Enterprise / EAP
- WireGuard peer tunnelling to AWS for site-to-site connectivity
- Separate WireGuard tunnel to ProtonVPN for isolated private traffic paths

This enables encrypted connectivity between home and cloud (Home ↔ AWS) while maintaining independent traffic routing for sensitive workloads.

**Security Model**

Defense-in-depth controls applied across every layer:

- No SSH exposed in AWS — SSM-first remote administration only
- WireGuard encrypted tunnels for all inter-site traffic
- Strict security groups and stateful firewall rules
- Cloudflare WAF protecting all public-facing services
- Least-privilege IAM roles — no broad or shared permissions
- VPN container isolation for torrent and download workloads
- Pi-hole DNS filtering blocking ads, trackers, and malicious domains

This mirrors the zero-trust security design patterns applied in enterprise environments.

{{< /tab >}}

{{< tab label="Automation & Ops" icon="code" >}}

**Automation and Lifecycle**

Automation is foundational to how this platform operates — not an afterthought added later.

- **Terraform** provisions all cloud infrastructure from code
- **Duin** automates container image update flows
- **Gatus** validates service availability with uptime checks and alerting
- **Beszel** collects continuous host and service metrics
- **GitHub** tracks all infrastructure changes and enables future CI/CD extension

**Operational Goals**

The platform is designed to:
- Detect failures quickly through monitoring and alerting
- Self-recover where automation can handle it
- Alert clearly when manual intervention is required
- Preserve full reproducibility — any component can be rebuilt from code

The objective is not automation for its own sake. The objective is reducing toil, catching failures early, and maintaining operational clarity.

{{< /tab >}}

{{< /tabs >}}

---

## Why Hybrid?

A pure cloud or pure on-prem approach would compromise either cost, control, or resilience. The hybrid model delivers all three:

- **Cloud** for internet-facing exposure, edge security, and elastic capability
- **On-prem** for private services, media workloads, and experimentation without cloud egress costs
- **WireGuard** for secure, unified networking that makes both layers behave as one
- **Full data ownership** with strong cost efficiency at scale
- **Real-world DevOps practice** in a controlled environment that mirrors enterprise architecture

---

## Lessons Applied

These principles emerged from operating this platform under real conditions — not from theory:

1. **Treat homelab systems like production from day one** — bad habits compound over time.
2. **Keep all infrastructure and changes in code** — memory and documentation both fail. Git doesn't.
3. **Do not expose services unless there is a clear, justified need** — default to closed.
4. **Build observability into the first version** — retrofitting monitoring is always harder and less complete.
5. **Automate upgrades, but always monitor outcomes** — automation without visibility is just faster failure.
6. **Network design and access policy matter more than raw compute** — segmentation prevents lateral movement.

---

## Closing Thoughts

This hybrid homelab is a continuously evolving engineering platform — not a static setup. It is where infrastructure as code, secure networking, containerized workloads, edge security controls, and automated operations are refined through real operational pressure.

The objective is not complexity. The objective is **clarity, resilience, and control**.
