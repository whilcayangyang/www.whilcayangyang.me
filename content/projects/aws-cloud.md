---
title: "AWS Implementation & Cost Optimization"
description: "AWS architecture redesign focused on performance, security, governance, and measurable cost efficiency."
weight: 20
showTableOfContents: true
draft: false
---

## Scholastic Asia AWS Implementation & Cost Optimization

{{< lead >}}
A full AWS modernization program that moved the environment from reactive, ad-hoc cloud operations to structured, policy-driven infrastructure — with stronger security, lower costs, and auditable governance at every layer.
{{< /lead >}}

{{< keywordList >}}
{{< keyword icon="cloud" >}} AWS Architecture {{< /keyword >}}
{{< keyword icon="code" >}} Terraform IaC {{< /keyword >}}
{{< keyword icon="github" >}} CI/CD Governance {{< /keyword >}}
{{< keyword icon="scale-balanced" >}} Cost Optimization {{< /keyword >}}
{{< keyword icon="shield" >}} Security Hardening {{< /keyword >}}
{{< /keywordList >}}

<div class="mt-4">
{{< alert icon="circle-info" >}}
**Objective:** Redesign AWS architecture for performance, security, governance, and sustainable cost efficiency — replacing manual operations with policy-driven, code-first infrastructure delivery.
{{< /alert >}}
</div>

---

## Program Scope

This initiative modernized cloud foundations across networking architecture, provisioning standards, and cost governance controls. The environment moved from reactive operations — where changes were made manually and inconsistently — to a structured, policy-driven cloud platform where every change is deliberate, validated, and traceable.

---

## Modernization Workstreams

{{< tabs group="scholastic-aws" >}}

{{< tab label="VPC Redesign" icon="globe" >}}

**Network Architecture Restructuring**

The existing VPC architecture had accumulated technical debt — overly permissive security groups, flat subnet design, and weak boundary enforcement. The redesign addressed all of it.

Key changes implemented:
- Clear separation of public and private subnets with appropriate routing boundaries
- Hardened route table design and explicit path controls between zones
- Optimized S3 gateway endpoint connectivity to eliminate unnecessary data egress
- Enforced stricter Network ACL policies at the subnet boundary level
- Applied least-privilege Security Group rules across all compute resources

**Outcome**

Reduced exposure risk and lateral movement surface, improved workload isolation between tiers, and established a stronger, auditable network security baseline.

{{< /tab >}}

{{< tab label="Infrastructure as Code" icon="code" >}}

**Terraform-Driven Delivery Model**

Manual infrastructure changes were replaced with a structured Terraform-based delivery model — every resource defined in code, every change going through a controlled pipeline.

Implementation:
- Rebuilt infrastructure using modular Terraform design patterns for reusability and clarity
- Source stored in Bitbucket with full change history and access controls
- Terraform plans executed through CI/CD pipelines — no manual console changes permitted

**Governance Effect**

Every infrastructure change passed automated validation before reaching the environment. This eliminated configuration drift, reduced human error, and created a complete audit trail of every change made to the platform.

**Outcome**

Infrastructure became predictable, reproducible, and safe to change — the foundation for all future platform growth.

{{< /tab >}}

{{< tab label="Cost Optimization" icon="scale-balanced" >}}

**Systematic Cost Reduction**

Cloud costs had grown without governance controls. A structured audit and optimization program addressed both recurring waste and architectural inefficiency.

Optimization actions taken:
- Applied EC2 Savings Plans to committed workloads for significant compute discounts
- Right-sized instance types based on actual utilization data — not assumed capacity
- Removed unused Elastic IP addresses accumulating idle charges
- Cleaned orphaned snapshots that were consuming storage without purpose
- Deleted stale AMIs no longer referenced by any active infrastructure
- Removed unattached EBS volumes left behind by terminated instances
- Transitioned S3 backup data to Glacier storage class for long-term cost reduction

**Result**

Significant reduction in monthly AWS spend. Resource utilization efficiency improved, and cloud spending shifted from reactive consumption to managed, governed investment.

{{< /tab >}}

{{< tab label="Security Hardening" icon="shield" >}}

**Security Controls Strengthened**

Security improvements were applied across network policy, access governance, and attack surface reduction — not as a separate project, but embedded into the modernization itself.

Controls enforced:
- VPC Network ACL policy boundaries tightened at the subnet level
- Security Group rules reviewed and restricted to verified required access only
- Unused and orphaned resources removed to eliminate unnecessary attack surface
- IAM roles and policies audited and restructured under least-privilege principles
- Unmanaged or overly broad permissions revoked across all accounts

**Outcome**

The AWS environment became more structured, auditable, and defensible — operating under a consistent least-privilege model with clearly defined access boundaries and no unnecessary exposure.

{{< /tab >}}

{{< /tabs >}}

---

## Implementation Summary

| Domain | Actions Implemented | Measurable Value |
| --- | --- | --- |
| Network Architecture | Subnet separation, route hardening, ACL and Security Group enforcement | Better segmentation, reduced exposure, stronger workload isolation |
| Delivery Governance | Terraform modules + Bitbucket + CI/CD pipeline validation | No configuration drift, complete change audit trail, safer deployments |
| Cost Management | Savings Plans + lifecycle cleanup + storage class tiering | Lower recurring spend, improved utilization, governed cloud investment |
| Security Posture | IAM review + access boundary tightening + resource cleanup | More auditable, defensible environment under least-privilege model |

---

## Business Impact

1. **Improved platform reliability** through cleaner architecture boundaries and consistent configuration.
2. **Reduced security risk** through least-privilege enforcement, network hardening, and attack surface reduction.
3. **Lowered cloud run-rate cost** through structured, systematic optimization across compute, storage, and network resources.
4. **Established a repeatable governance model** for all future AWS growth — changes follow the same controlled, auditable process.

---

## Closing Notes

This implementation delivered a balanced AWS strategy: high-performing infrastructure, enforceable governance, stronger security controls, and sustainable cost efficiency — not as trade-offs against each other, but as outcomes that reinforce one another when the architecture is built right.
