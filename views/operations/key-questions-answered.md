# Operations View — Key Questions Answered

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Unknown_

## Key Questions Answered

### What is running and what must stay up?

833 resources are deployed across 6 of 7 known accounts, spanning 2 observed regions. Five workloads are inferred, four of which are considered significant; one system is also identified. The production-critical path includes the **Cloudox Demo Atlas Prod API** workload (Workload Prod Account `122122642149`, eu-central-1), which has a verified dependency on the **cloudox-demo-atlas-prod-pg** database instance — making that datastore the highest-priority resource to keep available. Infrastructure includes 15 VPCs, 63 subnets, and 1 load balancer across the estate.

Confidence: Verified (resource counts); Likely (workload groupings are inferred)

### Where are the single points of failure?

The most concrete single point of failure identified is **cloudox-demo-atlas-prod-pg** — a DBInstance with no Multi-AZ standby configured. A single availability zone failure would take the data tier of the Cloudox Demo Atlas Prod API offline. There is no evidence of a failover replica or automated promotion path in this section's package. The recommended action is to evaluate enabling a Multi-AZ standby immediately for this instance.

Confidence: Verified (`dependency_concern:architecture:cloudox-demo-atlas-prod-pg`, `modernization_opportunity:architecture:cloudox-demo-atlas-prod-pg`)

### How is connectivity and egress routed?

9 internet-facing access paths are observed across the estate. Confirmed internet gateways include:
- `igw-0d14f1dd4e54d5906` (eu-central-1, Workload Dev Account `110019496666`)
- `igw-00ed21b9a0e6596a8` (us-east-1, Audit Account `110019496666`)
- `igw-0567575921f471548` (us-east-1, Workload Dev Account `105769365151`)

Two API Gateway endpoints are observed as internet-facing entry points:
- `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`
- `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`

DynamoDB tables in eu-central-1 are accessed from within the environment across multiple accounts (`cloudox-demo-atlas-dev-items` in `105769365151`, `cloudox-demo-atlas-prod-items` in `122122642149`, `cloudox-demo-sandbox-events` and `cloudox-demo-sandbox-scratch` in `161388682021`). Routing details beyond internet gateway and API Gateway presence are covered in other sections of this view.

Confidence: Verified (internet gateways and API Gateway endpoints are provider-derived)

### Where are monitoring, logging, or backup gaps?

Two significant detection coverage gaps exist across the estate:

**GuardDuty** is enabled in only 1 of 6 scanned accounts. The 5 accounts without coverage are: Log Archive Account (`122980216815`), Workload Dev Account (`105769365151`), Workload Prod Account (`122122642149`), Management Account (`110319895932`), and Sandbox Ma Account (`161388682021`). Threat detection is absent from all workload-bearing accounts.

**IAM Access Analyzer** is similarly enabled in only 1 of 6 scanned accounts, with the same 5 accounts uncovered. External-access analysis is not running where it is most needed.

For backup/recovery: **cloudox-demo-atlas-prod-pg** has no Multi-AZ standby and no backup posture evidence is present in this section's package — this is a material recovery gap for the production data tier.

Confidence: Likely (GuardDuty and Access Analyzer gaps — `risk:security:aws-guardduty-detector`, `risk:security:aws-accessanalyzer-analyzer`); Verified (cloudox-demo-atlas-prod-pg single-AZ — `modernization_opportunity:architecture:cloudox-demo-atlas-prod-pg`)

### What is most likely to cause an operational incident?

In priority order:

1. **cloudox-demo-atlas-prod-pg single-AZ failure** — A zone outage takes the production API's data tier offline with no automatic failover. This is the highest-severity operational risk with a direct production impact path (`dependency_concern:architecture:cloudox-demo-atlas-prod-pg`).
2. **No GuardDuty in 5 accounts** — Active threats in Workload Prod, Workload Dev, Management, Sandbox, and Log Archive accounts will not generate alerts, delaying incident detection and response (`risk:security:aws-guardduty-detector`).
3. **No IAM Access Analyzer in 5 accounts** — Unintended external resource exposure in those accounts will go undetected until manually reviewed (`risk:security:aws-accessanalyzer-analyzer`).
4. **Broadly-privileged IAM roles in Sandbox** — Roles `cloudox-demo-sandbox-ci-admin` (`AROAAAAAAO5VMOEOZ70IX`) and `cloudox-demo-sandbox-unused-admin` (`AROAAAAADPCL3BVEXUDTH`) in Sandbox Ma Account (`161388682021`) carry names suggesting administrative scope. If compromised or misused, the blast radius could be large. Privilege breadth is inferred from naming only — not confirmed from policy data.

Confidence: Verified (items 1–3); Assumed (items 4 — privilege inferred from naming, not policy inspection)

### What should be confirmed with the environment owner?

Two open validation questions require owner input before action:

1. **`cloudox-demo-sandbox-ci-admin`** (role ID `AROAAAAAAO5VMOEOZ70IX`, Sandbox Ma Account `161388682021`): *Does this role require its current privilege level, and who owns it?* The name implies CI pipeline use with admin rights — confirm whether this is intentional and whether scope can be reduced (`validation_question:security:cloudox-demo-sandbox-ci-admin`).

2. **`cloudox-demo-sandbox-unused-admin`** (role ID `AROAAAAADPCL3BVEXUDTH`, Sandbox Ma Account `161388682021`): *Does this role require its current privilege level, and who owns it?* The name suggests it may be unused — confirm whether it can be deleted or scoped down (`validation_question:security:cloudox-demo-sandbox-unused-admin`).

Additionally, the backup and Multi-AZ configuration of **cloudox-demo-atlas-prod-pg** should be confirmed with the owner of the Workload Prod Account (`122122642149`) — no backup posture evidence is available in this section's package.

Confidence: Assumed (IAM role privilege breadth is inferred from naming only)
