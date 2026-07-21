# Executive View — Executive Summary

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Likely_

## Executive Summary

### The Environment in Brief

This is a multi-account AWS organisation spanning **4 active accounts** — Management, Workload Dev, Workload Prod, and Sandbox — plus a Log Archive account and one account excluded from scope. Discovery covered **833 resources across 2 regions** (primarily eu-central-1, with us-east-1 used for disaster-recovery infrastructure in the Workload Prod account).

Four significant workloads are in play: the core Atlas workload running in both Development and Production environments, a Sandbox workload, and the CloudoX platform itself. One additional workload was classified as a helper/governance concern rather than a business-facing service.

> **Confidence: Likely.** Classification relies on inference for the majority of resources — 781 of 833 carry no Environment/Stage/Tier tag. The picture is coherent but tagging gaps mean workload boundaries and environment assignments should be treated as directional rather than definitive.

### Overall Posture

Three areas warrant leadership attention:

**1. Internet exposure change (act now).** Since the previous snapshot, one security group (`sg-0d6a48061beb72eae`) became newly reachable from the internet. A separate security group (`sg-04fae132cfc68e91d`) lost internet reachability in the same period. The new exposure should be validated as intentional before the next review cycle; unplanned internet-facing changes are the most common precursor to security incidents.

**2. Disaster-recovery build-out in progress (positive signal, verify completeness).** A new CloudFormation DR stack was deployed into the Workload Prod account in us-east-1, accompanied by a CloudWatch alarm monitoring DR bucket size (`cloudox-demo-atlas-prod-dr-bucket-size`). This indicates active investment in resilience for the production Atlas workload. Engineering leadership should confirm the DR runbook and RTO/RPO targets are documented alongside this infrastructure.

**3. Observability and governance tooling now in scope.** Security Hub, Systems Manager (SSM), and Step Functions entered the discovered scope in this snapshot. Their presence suggests maturing operational controls, but their configuration depth and coverage are not yet assessed in this section — see the relevant Security and Architecture views for detail.

A new DynamoDB table (`cloudox-demo-sandbox-events`) was also added in the Sandbox account, consistent with active development activity there.

**Tagging and classification debt** remains a structural risk: without consistent tagging, cost attribution, blast-radius analysis, and access scoping all rely on inference. This is a decision that benefits from a top-down mandate.

### Changes Since Previous Snapshot

The following changes were observed between the snapshot at 11:50 UTC and the current snapshot at 12:54 UTC on 20 July 2026:

- **New internet exposure:** Security group `sg-0d6a48061beb72eae` became reachable from the internet — requires validation.
- **Removed internet exposure:** Security group `sg-04fae132cfc68e91d` is no longer internet-reachable.
- **New services in scope:** Security Hub, SSM, and Step Functions entered discovery.
- **DR infrastructure added:** A new CloudFormation DR stack and a CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`) were deployed in the Workload Prod account (us-east-1).
- **New Sandbox table:** `cloudox-demo-sandbox-events` DynamoDB table added in the Sandbox account.
- Two workloads tentatively grew in membership: 'cloudox' from 9 to 10 resources, and 'cloudox-demo-atlas-dev' from 4 to 5 resources.

More than 100 additional changes occurred in this period. See the **Environment Evolution** page for the full picture.
