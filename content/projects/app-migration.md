---
title: "Retiring Deprecated EC2: Windows Server Modernization with Zero Application Downtime"
description: "End-to-end migration of a business-critical application from deprecated Windows Server EC2 instances to Windows Server 2022 — covering fresh IIS and SQL Server provisioning, database migration, security hardening, and structured environment promotion."
weight: 23
showTableOfContents: true
draft: false
---

## EC2 Windows Server Modernization: IIS and SQL Server Migration to Windows Server 2022

{{< lead >}}
A full-stack migration of a business-critical application running on deprecated Windows Server EC2 instances to Windows Server 2022 — provisioned fresh, hardened from the OS up, and promoted through three gated environments with zero unplanned downtime at cutover.
{{< /lead >}}

{{< keywordList >}}
{{< keyword icon="cloud" >}} AWS EC2 · Windows Server 2022 {{< /keyword >}}
{{< keyword icon="code" >}} Terraform IaC {{< /keyword >}}
{{< keyword icon="server" >}} IIS + SQL Server Migration {{< /keyword >}}
{{< keyword icon="shield" >}} Security Hardening {{< /keyword >}}
{{< keyword icon="check" >}} Zero Downtime Cutover {{< /keyword >}}
{{< /keywordList >}}

<div class="mt-4">
{{< alert icon="circle-info" >}}
**Objective:** Replace deprecated Windows Server EC2 instances with a clean Windows Server 2022 environment — migrating IIS and SQL Server workloads, hardening the OS and application stack, and delivering IaC-backed infrastructure with full operational runbooks.
{{< /alert >}}
</div>

---

## The Problem

The application stack was running on EC2 instances with a deprecated Windows Server version — past end-of-support, no longer receiving security patches, and carrying the compliance and operational risk that comes with an EOL OS in production. The SQL Server and IIS configurations had accumulated undocumented changes over time, making the environment increasingly fragile.

A side-by-side rebuild was the right call over an in-place upgrade: a fresh Windows Server 2022 environment could be hardened from scratch, validated independently in staging, and cut over to without risking the existing production instance until the new one was confirmed ready.

The brief was clear — no service interruption, no inherited configuration debt, and no undocumented environment handed to the operations team at the end.

---

## Architecture

The new environment mirrors the existing AWS topology but on current, supported infrastructure. The deprecated EC2 instances are replaced by Windows Server 2022 equivalents, provisioned and configured from code.

| Tier | Design Decision | Why |
|------|----------------|-----|
| Compute | Fresh EC2 instances on Windows Server 2022 | Clean OS baseline — no inherited configuration drift from the deprecated environment |
| Load Balancer | Application Load Balancer with TLS termination via ACM | Certificate lifecycle automated; new instances registered without DNS disruption |
| Application | IIS on Windows Server 2022 in private subnets | ALB-only ingress enforced — instances not directly internet-reachable |
| Database | SQL Server Standard on Windows Server 2022, isolated private subnet | Port-restricted security group — accessible only from the application tier |
| DNS | Route 53 | Cutover managed at DNS level — rollback possible without infrastructure changes |
| Secrets | AWS Secrets Manager | Credentials removed from application config files and AMIs |

---

## Delivery Workstreams

{{< tabs group="app-migration" >}}

{{< tab label="Infrastructure Build" icon="server" >}}

**Fresh EC2 — Built from Code, Hardened Before Application Deployment**

New Windows Server 2022 EC2 instances were provisioned through Terraform — no manual console clicks, no inherited configuration from the deprecated environment. OS-level hardening was applied before any application software was installed:

- Unnecessary Windows roles and services disabled
- Remote access locked down to operational requirements only
- Windows Firewall rules configured per application tier
- Default IIS and server response headers stripped to prevent version disclosure

**SQL Server Migration**

SQL Server was installed and configured to production-grade standards on the new instances:

- Production maintenance jobs, backup schedules, and SQL Agent configured from scratch
- SSIS installed and registered for data integration workflows
- Logins, linked servers, and SQL Agent job definitions migrated from the deprecated EC2
- Backup and restore procedures validated end-to-end before promotion to UAT
- Data integrity verified through row count checks and key report reconciliation

**IIS Migration**

IIS was rebuilt on Windows Server 2022 and configured to operate correctly behind the Application Load Balancer:

- Application pools with correct .NET runtime bindings
- URL Rewrite module installed to propagate `X-Forwarded-For` client IP headers from the ALB
- Application deployment process documented and validated in Dev before UAT promotion
- Response header hardening applied to remove version information from HTTP responses

{{< /tab >}}

{{< tab label="Environment Pipeline" icon="check" >}}

**Three Gated Environments — No Skipping Stages**

The migration ran across three environments with explicit acceptance criteria and team sign-off at each gate. The deprecated EC2 instances stayed in place throughout — the new Windows Server 2022 environment was validated entirely before any DNS cutover happened.

**Development**

Fresh EC2 instances stood up, SQL Server and IIS configured, application deployed. Smoke tests run to confirm the new environment was functionally equivalent to the deprecated one. No UAT promotion until this stage was clean.

**UAT / Staging**

Full functional and regression testing per application module. Performance baselines captured on the new Windows Server 2022 instances:
- CPU and memory utilization under representative load
- Disk latency under write-heavy SQL workloads
- SQL wait statistics compared against the deprecated environment baseline
- IIS response times per endpoint

Data validation checks confirmed migrated SQL data matched the source exactly — row counts, key reports, and reconciliation outputs all verified before production sign-off.

**Production**

Cutover executed against a documented runbook. DNS was the cutover lever — traffic shifted to the new instances at the Route 53 level, with rollback possible by pointing DNS back to the deprecated EC2s without touching infrastructure. The runbook included explicit pass/fail validation criteria and sign-off gates at each phase.

{{< /tab >}}

{{< tab label="Infrastructure as Code" icon="code" >}}

**Terraform — Every Resource, Every Change**

All EC2 instances, security groups, IAM roles, and supporting services are defined in Terraform, maintained in a Bitbucket repository, and deployed through a CI/CD pipeline to Terraform Cloud. Branching follows `feature → dev → main`.

This matters for a migration specifically because:
- The new environment is reproducible from code — not a one-time manual build that can't be reconstructed
- Configuration differences between Dev, UAT, and Production are visible in the Terraform codebase, not hidden in console state
- If the deprecated environment needs to be stood up again for any reason, the old configuration is in version history
- Any future Windows Server upgrade follows the same pipeline — not another undocumented manual rebuild

Changes that don't pass Terraform Cloud validation don't reach the environment.

{{< /tab >}}

{{< tab label="Documentation" icon="book-open" >}}

**Runbooks — Not Tribal Knowledge**

The deprecated environment's configuration had accumulated over time without documentation. The new environment was built in the opposite direction — documentation written alongside the build, stored in the central repository with versioning and approval workflow.

Coverage:
- Network architecture — VPC, subnets, security groups, routing, DNS
- EC2 build — Windows Server 2022 configuration steps and OS hardening checklist
- SQL Server — installation settings, maintenance jobs, backup and restore procedures, migration steps from the deprecated instance
- IIS — feature installation, app pool configuration, bindings, deployment process
- Cutover plan — rollback procedure, DNS cutover steps, validation checklist, sign-off gates
- Monitoring — baseline metrics and alerting configuration

The operations team inherits a living document, not a dependency on the people who ran the migration.

{{< /tab >}}

{{< /tabs >}}

---

## Key Challenges

{{< alert icon="triangle-exclamation" cardColor="#1e1e2e" iconColor="#f38ba8" textColor="#cdd6f4" >}}
**SSIS Subsystem Registration on Windows Server 2022 AMI**
SQL Server Integration Services installed without errors but failed to register in SQL Agent's subsystem table on the provisioned AMI. Standard SSIS installation documentation does not cover this. Resolution required a manual insertion into the `msdb` subsystem table and a SQL Agent restart before SSIS job step types became selectable. This would have silently broken SSIS-dependent jobs post-cutover if not caught in Dev.
{{< /alert >}}

{{< alert icon="triangle-exclamation" cardColor="#1e1e2e" iconColor="#f38ba8" textColor="#cdd6f4" >}}
**ALB and IIS Header Propagation**
IIS on Windows Server 2022 does not natively process `X-Forwarded-For` headers injected by the Application Load Balancer. Without the URL Rewrite module configured, every client IP in application and access logs shows the ALB's internal IP — not the originating client. The problem is invisible until you check logs after cutover. URL Rewrite configuration was validated explicitly during UAT.
{{< /alert >}}

{{< alert icon="triangle-exclamation" cardColor="#1e1e2e" iconColor="#f38ba8" textColor="#cdd6f4" >}}
**Configuration Drift Across Environments**
SQL Server job definitions, linked server configurations, and application connection strings drift quietly across environments when promotion is informal. A structured promotion checklist with explicit configuration tracking was required to catch discrepancies between Dev, UAT, and Production before they caused failures at cutover.
{{< /alert >}}

---

## Outcomes

| Outcome | Detail |
|---------|--------|
| Deprecated OS retired | Business-critical application moved off EOL Windows Server to Windows Server 2022 — patched, supported, and compliant |
| Zero unplanned downtime | Production cutover executed via DNS with rollback ready — deprecated EC2s remained available throughout the cutover window |
| Clean security baseline | Fresh OS build hardened before application deployment; credentials moved to Secrets Manager; server headers stripped |
| Reproducible infrastructure | New environment is fully defined in Terraform — any instance can be rebuilt from code, not reconstructed from memory |
| Operational handover | Full runbook coverage for every component — the team inherited documentation, not a dependency on the migration team |

---

## Technologies Used

{{< keywordList >}}
{{< keyword icon="cloud" >}} AWS EC2 · ALB · Route 53 {{< /keyword >}}
{{< keyword icon="lock" >}} ACM · Secrets Manager {{< /keyword >}}
{{< keyword icon="eye" >}} CloudWatch {{< /keyword >}}
{{< keyword icon="server" >}} Windows Server 2022 · IIS · SQL Server Standard {{< /keyword >}}
{{< keyword icon="code" >}} Terraform · Terraform Cloud {{< /keyword >}}
{{< keyword icon="github" >}} Bitbucket · CI/CD Pipelines {{< /keyword >}}
{{< keyword icon="terminal" >}} PowerShell · SSIS {{< /keyword >}}
{{< /keywordList >}}
