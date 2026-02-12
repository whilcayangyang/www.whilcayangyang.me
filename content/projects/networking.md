---
title: "Scholastic Asia Network Modernization Program"
description: "Cisco and Palo Alto implementation across five Scholastic Asia sites with segmentation, NAC, SIEM integration, and SOC enablement."
showTableOfContents: true
draft: false
---

## Scholastic Asia Cisco / Palo Alto Network Implementation (2023)

{{< lead >}}
A full-stack enterprise network modernization across five Scholastic Asia locations, redesigning topology, upgrading security controls, and enabling centralized monitoring and SOC visibility.
{{< /lead >}}

{{< keywordList >}}
  {{< keyword icon="globe" >}}5 Countries{{< /keyword >}}
  {{< keyword icon="shield" >}}Palo Alto NGFW{{< /keyword >}}
  {{< keyword icon="lock" >}}FortiNAC{{< /keyword >}}
  {{< keyword icon="eye" >}}SolarWinds + QRadar{{< /keyword >}}
  {{< keyword icon="check" >}}Resilience + Segmentation{{< /keyword >}}
{{< /keywordList >}}

{{< alert icon="circle-info" >}}
**Objective:** Redesign the network architecture across Scholastic Asia from hardware through configuration, increase security posture, centralize visibility, and remove single points of failure.
{{< /alert >}}

## Regional Scope

Countries covered:

- Malaysia
- Singapore
- India
- China
- Philippines

## Program Goals

1. Modernize core and distribution network topology.
2. Standardize infrastructure and security policy across all sites.
3. Improve resilience with redundancy and dual-path architecture.
4. Strengthen access control, threat detection, and SOC readiness.
5. Centralize monitoring and logging for operational visibility.

## Modernization Workstreams

{{< tabs group="asia-network-modernization" default="Topology Redesign" >}}
{{< tab label="Topology Redesign" icon="globe" >}}
**Implementation Scope**

- Core and distribution redesign across all sites
- IP re-segmentation and subnet standardization
- Cable redundancy and dual-path resilience
- Hardware refresh and configuration standardization

**Architecture Improvements**

- Migrated from legacy flat networks to structured VLAN segmentation
- Isolated user, server, management, voice, and guest networks

**Outcome**

- Reduced lateral movement risk
- Improved operational clarity and fault isolation
- Increased network reliability under failure conditions
{{< /tab >}}

{{< tab label="Firewall Modernization" icon="shield" >}}
**Security Platform Migration**

- Replaced legacy Cisco ASA with Palo Alto Networks NGFW
- Rebuilt inter-site connectivity with IPSEC site-to-site tunnels

**Key Security Gains**

- Application-aware filtering
- Granular policy enforcement
- Advanced threat prevention
- SSL inspection capability
- Centralized policy governance

**Outcome**

- Unified security perimeter across all Scholastic Asia offices
- Stronger encrypted communication between regions
- Better policy consistency and auditability
{{< /tab >}}

{{< tab label="Access Control" icon="lock" >}}
**Layer 2 Security Deployment**

- Implemented Network Access Control using Fortinet FortiNAC

**Capabilities Enabled**

- Device authentication before network access
- Unauthorized device quarantine
- Role-based access enforcement
- Endpoint compliance validation

**Outcome**

- Shifted security posture from reactive to preventative
- Reduced unauthorized access risk at the edge
{{< /tab >}}

{{< tab label="Monitoring & SOC" icon="eye" >}}
**Visibility and Detection Integration**

- Integrated full device telemetry into SolarWinds
- Forwarded firewall traffic logs to IBM QRadar
- Enabled SOC workflows for vulnerability scanning and detection

**QRadar Benefits**

- Centralized log correlation
- Suspicious traffic analysis
- Threat intelligence mapping
- Vulnerability detection across network devices

**Outcome**

- Transitioned from infrastructure-managed to security-observable operations
- Improved incident triage and detection readiness
{{< /tab >}}
{{< /tabs >}}

## Security and Operations Model

| Domain | Legacy State | Modernized State |
| --- | --- | --- |
| Segmentation | Flat network segments | VLAN-based segmented architecture |
| Perimeter Security | Cisco ASA | Palo Alto NGFW with centralized governance |
| Inter-Site Connectivity | Mixed legacy tunnels | Standardized IPSEC tunnels |
| Access Control | Limited edge validation | FortiNAC with device and role enforcement |
| Monitoring | Partial network visibility | SolarWinds + QRadar integrated telemetry |
| SOC Readiness | Limited correlation | Centralized detection and analysis pipeline |

## Business and Technical Impact

- Increased network resilience across five countries
- Reduced attack surface through segmentation and policy hardening
- Eliminated legacy firewall risk with NGFW modernization
- Established centralized monitoring and SIEM integration
- Strengthened SOC readiness and response capability

## Closing Summary

This program delivered a coordinated modernization of topology, firewall architecture, access control, and observability. The result is a standardized, resilient, and security-first regional network foundation designed for scale and continuous operations.
