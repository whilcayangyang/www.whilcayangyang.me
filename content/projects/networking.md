---
title: "Five-Country Network Overhaul: Cisco, Palo Alto, and SOC-Ready Infrastructure"
description: "Cisco and Palo Alto implementation across five Scholastic Asia sites with segmentation, NAC, SIEM integration, and SOC enablement."
weight: 24
showTableOfContents: true
draft: false
---

## Scholastic Asia Cisco / Palo Alto Network Implementation (2023)

{{< lead >}}
A full-stack enterprise network modernization across five Scholastic Asia locations — redesigning topology from the ground up, replacing legacy security platforms, and establishing centralized visibility and SOC-ready detection capability.
{{< /lead >}}

{{< keywordList >}}
{{< keyword icon="globe" >}} 5 Countries {{< /keyword >}}
{{< keyword icon="shield" >}} Palo Alto NGFW {{< /keyword >}}
{{< keyword icon="lock" >}} FortiNAC {{< /keyword >}}
{{< keyword icon="eye" >}} SolarWinds + QRadar {{< /keyword >}}
{{< keyword icon="check" >}} Resilience + Segmentation {{< /keyword >}}
{{< /keywordList >}}

<div class="mt-4">
{{< alert icon="circle-info" >}}
**Objective:** Redesign the network architecture across Scholastic Asia from hardware through configuration — increase security posture, centralize visibility, and eliminate single points of failure across all five sites.
{{< /alert >}}
</div>

---

## Regional Scope

This program covered simultaneous implementation across five countries, each requiring site-specific execution while maintaining a consistent architectural standard:

- Malaysia
- Singapore
- India
- China
- Philippines

Coordinating across five countries, multiple time zones, and diverse local network conditions required structured program management alongside deep technical execution.

---

## Program Goals

1. Modernize core and distribution network topology across all sites.
2. Standardize infrastructure and security policy to a single consistent baseline.
3. Improve resilience with redundancy and dual-path architecture at each location.
4. Strengthen access control, threat detection, and SOC readiness.
5. Centralize monitoring and logging for unified operational visibility.

---

## Modernization Workstreams

{{< tabs group="asia-network-modernization" >}}

{{< tab label="Topology Redesign" icon="globe" >}}

**Network Architecture Restructuring**

The legacy network across all five sites operated as flat, largely undifferentiated segments — a design that created both security risk and operational complexity. The redesign addressed the architecture at every layer.

Implementation scope:
- Core and distribution topology redesign across all sites simultaneously
- IP re-segmentation and subnet standardization for consistency across countries
- Physical cable redundancy and dual-path resilience engineered into the architecture
- Hardware refresh paired with configuration standardization to eliminate legacy drift

Architecture improvements delivered:
- Migration from legacy flat networks to structured, zone-based VLAN segmentation
- Dedicated isolation of user, server, management, voice, and guest network zones
- Elimination of lateral movement paths between user and server segments

**Outcome**

Reduced lateral movement risk across all sites, improved operational clarity and fault isolation, and significantly increased network reliability under failure conditions.

{{< /tab >}}

{{< tab label="Firewall Modernization" icon="shield" >}}

**Security Platform Migration**

Legacy Cisco ASA firewalls were replaced with Palo Alto Networks Next-Generation Firewalls — a fundamental shift in security capability, not just a hardware refresh.

Migration scope:
- Decommissioned legacy Cisco ASA platforms across all five locations
- Deployed Palo Alto NGFW with consistent policy baseline across sites
- Rebuilt inter-site connectivity with standardized IPSEC site-to-site tunnels

Security capabilities unlocked:
- Application-aware traffic filtering — decisions based on application, not just port
- Granular policy enforcement down to user and application identity
- Advanced threat prevention with signature and behavioral detection
- SSL inspection for encrypted traffic visibility
- Centralized policy governance and consistent rule management across all sites

**Outcome**

Unified security perimeter across all five Scholastic Asia offices, stronger encrypted inter-site communication, and dramatically improved policy consistency and auditability.

{{< /tab >}}

{{< tab label="Access Control" icon="lock" >}}

**Network Access Control Deployment**

Layer 2 access control was implemented using **Fortinet FortiNAC** — shifting device access policy from a reactive, implicit trust model to proactive, identity-verified enforcement.

Capabilities enforced:
- Device authentication required before any network access is granted
- Unauthorized or unmanaged devices automatically quarantined
- Role-based network access enforcement based on device identity and compliance status
- Endpoint compliance validation before access to sensitive network zones

**Outcome**

Security posture shifted from reactive detection to preventative enforcement at the network edge — unauthorized devices are blocked before they reach any internal resource, not discovered after the fact.

{{< /tab >}}

{{< tab label="Monitoring & SOC" icon="eye" >}}

**Visibility and Detection Integration**

Network visibility was established by integrating infrastructure telemetry into centralized monitoring and SIEM platforms, enabling Security Operations Center workflows across all five countries.

Integration delivered:
- Full network device telemetry integrated into **SolarWinds** for operational health monitoring
- Palo Alto NGFW traffic logs forwarded to **IBM QRadar** for security event correlation
- SOC workflows enabled for vulnerability scanning, suspicious traffic investigation, and detection
- Packet-level visibility available for incident triage and forensic investigation

QRadar capabilities enabled:
- Centralized log correlation across network, firewall, and infrastructure sources
- Suspicious traffic pattern analysis with rule-based alerting
- Threat intelligence mapping against observed network behaviour
- Vulnerability detection correlated across all monitored network devices

**Outcome**

Transitioned from infrastructure-managed operations with fragmented visibility to a SOC-observable network where threats can be detected, investigated, and responded to from a centralized platform.

{{< /tab >}}

{{< /tabs >}}

---

## Security and Operations Model

| Domain | Legacy State | Modernized State |
| --- | --- | --- |
| Segmentation | Flat network segments with minimal zone isolation | VLAN-based segmented architecture with enforced zone boundaries |
| Perimeter Security | Cisco ASA with limited application awareness | Palo Alto NGFW with application-aware policy and centralized governance |
| Inter-Site Connectivity | Mixed legacy tunnels with inconsistent configuration | Standardized IPSEC tunnels with consistent policy across all sites |
| Access Control | Limited edge validation, implicit device trust | FortiNAC with device authentication and role-based enforcement |
| Monitoring | Partial network visibility, no centralized correlation | SolarWinds + IBM QRadar integrated telemetry and SIEM correlation |
| SOC Readiness | Limited detection, no centralized analysis pipeline | Centralized detection, correlation, and investigation capability |

---

## Business and Technical Impact

- **Increased network resilience** across five countries through redundant architecture and dual-path design
- **Reduced attack surface** through VLAN segmentation, policy hardening, and zero-trust access enforcement
- **Eliminated legacy firewall risk** by replacing Cisco ASA with Palo Alto NGFW across all sites
- **Established centralized monitoring** through SolarWinds and IBM QRadar SIEM integration
- **Strengthened SOC readiness** with the detection, correlation, and investigation infrastructure to support active security operations

---

## Closing Summary

This program delivered a coordinated, multi-country modernization of topology, firewall architecture, access control, and security observability. The result is a **standardized, resilient, and security-first regional network foundation** — designed for scale, continuous operations, and evolving threat response across all five Scholastic Asia sites.
