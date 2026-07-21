# Architect View — Dependencies

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Likely_

## Dependencies

The most actionable finding in this section is a verified single-AZ database dependency sitting in the critical path of a production workload — a resilience gap that warrants immediate architectural review. The broader dependency picture spans 15 VPCs, 63 subnets, and 9 observed internet-facing access paths across multiple workloads and accounts.

### Workload Dependencies

Six workloads are identified across three accounts in eu-central-1:

| Workload | Account | Confidence |
|---|---|---|
| Cloudox (`cloudox`) | 122122642149 | Verified |
| Cloudox Demo Atlas Prod API (`cloudox-demo-atlas-prod-api`) | 122122642149 | Likely |
| Cloudox Demo Atlas Dev (`cloudox-demo-atlas-dev`) | 105769365151 | Likely |
| Cloudox Demo Atlas Dev API (`cloudox-demo-atlas-dev-api`) | 105769365151 | Likely |
| Cloudox Demo Sandbox Scratch (`cloudox-demo-sandbox-scratch`) | 161388682021 | Assumed |
| Cloudox Demo Atlas Dev (system) | Unknown | Assumed |

Two internet-facing API Gateway endpoints are observed: `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com` and `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`. These represent externally reachable entry points into the workload graph. A total of 9 internet-facing access paths are recorded across the environment.

Note: workloads marked **Assumed** rely on inference rather than explicit tagging — 781 resources carry no Environment / Stage / Tier tag, which limits classification confidence for those entities.

### Data Dependencies

Four DynamoDB tables are observed as data-tier dependencies across accounts and stages:

| Table | Account | Stage |
|---|---|---|
| `cloudox-demo-atlas-dev-items` | 105769365151 | Dev |
| `cloudox-demo-atlas-prod-items` | 122122642149 | Prod |
| `cloudox-demo-sandbox-events` | 161388682021 | Sandbox |
| `cloudox-demo-sandbox-scratch` | 161388682021 | Sandbox |

Beyond DynamoDB, the **Cloudox Demo Atlas Prod API** workload (`cloudox-demo-atlas-prod-api`, account 122122642149, eu-central-1) has a verified dependency on the RDS DB instance **cloudox-demo-atlas-prod-pg**. This instance is not Multi-AZ. A zone failure would take the production data tier offline for this workload — the highest-priority dependency concern identified in this section.

> **Dependency concern** (`dependency_concern:architecture:cloudox-demo-atlas-prod-pg`, Priority 3, Confidence: Verified): *"A workload depends on datastore 'cloudox-demo-atlas-prod-pg', which is not Multi-AZ; a zone failure would take the dependent workload's data tier offline."*
>
> **Recommended action:** Confirm resilience posture (Multi-AZ enablement and/or backup strategy) for `cloudox-demo-atlas-prod-pg`, and review how the Cloudox Demo Atlas Prod API handles a data-tier failure — including connection retry logic, circuit-breaking, and graceful degradation.

Account-level details for `cloudox-demo-atlas-prod-pg` itself are not resolved in this package (account ID and region are unknown for the DB instance resource directly).

### Coupling Risks

The primary structural coupling risk is the single-AZ RDS instance described above — a hard dependency from a production API workload to a data tier with no observed zone redundancy.

Internet gateway presence across multiple accounts and regions (internet gateways observed in us-east-1 account 110019496666, eu-central-1 account 110019496666, and us-east-1 account 105769365151) indicates that internet egress/ingress paths exist beyond the eu-central-1 production footprint. The architectural significance of cross-region gateways relative to the workloads in scope is not fully resolved from this section's evidence.

Security group exposure changes (see below) are relevant to coupling risk: a newly internet-reachable security group (`sg-0d6a48061beb72eae`) may expand the attack surface of any resource it protects, depending on what it is attached to — that attachment is not resolved in this package.

### Changes Since Previous Snapshot

Between the snapshot at 2026-07-20T11:50 UTC and the current snapshot at 2026-07-20T12:54 UTC, two observed exposure changes occurred:

- **Added:** Security group `sg-0d6a48061beb72eae` became reachable from the internet. Architects should confirm which resources this group protects and whether this exposure is intentional.
- **Removed:** Security group `sg-04fae132cfc68e91d` is no longer reachable from the internet — a reduction in internet exposure.

Additional related changes exist beyond those listed here; see the Environment Evolution page for the full picture.
