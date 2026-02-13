---
title: "Building an Enterprise-Grade Hybrid Homelab"
description: "Cloud + on-prem architecture with AWS, Cloudflare, Linux, WireGuard, and infrastructure automation."
weight: 40
showTableOfContents: true
draft: false
---

## Cloud + On-Prem Architecture with AWS, Cloudflare, and Linux

{{< lead >}}
Modern infrastructure should be portable, observable, secure, and automated. This homelab is designed as a production-grade hybrid environment, with enterprise-grade cloud architecture powering this platform.
{{< /lead >}}

{{< keywordList >}}
  {{< keyword icon="cloud" >}}AWS + Cloudflare{{< /keyword >}}
  {{< keyword icon="globe" >}}Hybrid Networking{{< /keyword >}}
  {{< keyword icon="shield" >}}Zero-Trust Security{{< /keyword >}}
  {{< keyword icon="code" >}}Infrastructure as Code{{< /keyword >}}
  {{< keyword icon="eye" >}}Observability{{< /keyword >}}
  {{< keyword icon="check" >}}Automation{{< /keyword >}}
{{< /keywordList >}}

<div class="mt-4">
{{< alert icon="circle-info" >}}
**Design Principle:** Treat the homelab like production. Every component is version-controlled, automated where possible, and secured by default.
{{< /alert >}}
</div>

## Architecture Overview

The environment combines:

- Cloud Layer: AWS + Cloudflare for internet-facing services
- On-Prem Layer: Beelink server + MikroTik router for local workloads
- Secure Interconnect: WireGuard tunnels between home and cloud
- Platform Model: Docker workloads managed and monitored continuously
- Infrastructure Delivery: Terraform + GitHub for reproducible provisioning

Everything is tracked in GitHub to keep deployments deterministic and auditable.

## Platform Breakdown

{{< tabs group="homelab-stack" >}}
{{< tab label="Cloud Layer" icon="cloud" >}}
### AWS Infrastructure

All AWS and Cloudflare resources are provisioned through **Terraform** and version-controlled in **GitHub**.

Outcomes:

- Deterministic deployments
- Full infrastructure traceability
- Rapid rebuild capability
- Reduced configuration drift

### EC2 Baseline

- Hardened EC2 hosts
- Debian 13
- Docker runtime
- WireGuard peer to on-prem network

Debian is used for long-term stability, minimal overhead, and predictable operations.

### Container Stack

- Traefik: Reverse proxy and dynamic routing
- NGINX: Web serving
- Gatus: Availability monitoring
- Beszel: System monitoring
- Duin: Automated container upgrades
- Pi-hole: DNS filtering
- Portainer: Container lifecycle management
- Vaultwarden: Self-hosted password manager

### Core AWS Services

- VPC, Security Groups, and NACLs for segmentation and access control
- S3 for object storage
- SES for transactional email notifications
- IAM for role-based access control
- SSM for secure remote administration without exposing SSH

Using SSM instead of public SSH substantially lowers attack surface.

### Cloudflare Edge

- Domain and DNS management
- WAF for Layer 7 protection
- CDN for low-latency delivery
- Workers for lightweight edge logic

Cloudflare adds DDoS mitigation, TLS termination, and intelligent edge enforcement.
{{< /tab >}}

{{< tab label="On-Prem Layer" icon="itch-io" >}}
### Local Infrastructure

Primary node:

- Beelink SER8
- Debian 13
- Docker runtime

Primary roles:

- Media services
- Local automation hub
- Secondary service environment
- Hybrid cloud peer

### On-Prem Container Services

- Traefik (local routing)
- Beszel agent
- Duin (automated upgrades)
- Portainer

Streaming stack:

- Jellyfin
- Bazarr
- Prowlarr
- Radarr
- qBittorrent
- Gluetun (VPN isolation)
- PairDrop (local file sharing)

The streaming pipeline is isolated behind Gluetun for safer network boundaries.
{{< /tab >}}

{{< tab label="Network & Security" icon="shield" >}}
### Network Core

MikroTik hAP ax3 responsibilities:

- Routing and segmentation
- Stateful firewall policy
- DHCP services
- Secure Wi-Fi (WPA Enterprise / EAP)
- WireGuard peer to AWS
- Separate WireGuard tunnel to ProtonVPN

This enables encrypted site-to-site connectivity (Home <-> AWS) and separate private traffic paths.

### Security Model

Defense-in-depth controls include:

- No exposed SSH in AWS (SSM-first administration)
- WireGuard encrypted tunnels
- Strict security groups and firewall rules
- Cloudflare WAF for public services
- Least-privilege IAM roles
- VPN isolation for torrent workloads
- DNS filtering with Pi-hole

This mirrors enterprise zero-trust design patterns.
{{< /tab >}}

{{< tab label="Automation & Ops" icon="code" >}}
### Automation and Lifecycle

Automation is foundational:

- Terraform provisions infrastructure
- Duin handles container update flows
- Gatus validates service availability
- Beszel collects host and service metrics
- GitHub tracks changes and enables future CI/CD extensions

Operational goals:

- Detect failures quickly
- Self-recover where appropriate
- Alert when manual intervention is required
- Preserve reproducibility across environments
{{< /tab >}}
{{< /tabs >}}

## Why Hybrid?

This architecture balances cloud resilience with on-prem control:

- Cloud for public exposure and elastic scaling
- On-prem for private services and experimentation
- WireGuard for secure, unified networking
- Strong cost efficiency with full data ownership
- Real-world DevOps practice in a controlled environment

## Lessons Learned

1. Treat homelab systems like production from day one.
2. Keep infrastructure and changes in code.
3. Do not expose services unless there is a clear need.
4. Build observability into the first version, not as an afterthought.
5. Automate upgrades, but always monitor outcomes.
6. Network design and policy matter more than raw compute.

## Closing Thoughts

This hybrid homelab is a continuously evolving platform for refining cloud engineering and DevOps practices across:

- Infrastructure as Code
- Secure networking
- Containerized workloads
- Edge security controls
- Monitoring and observability
- Automated operations

The objective is not complexity. The objective is clarity, resilience, and control.
