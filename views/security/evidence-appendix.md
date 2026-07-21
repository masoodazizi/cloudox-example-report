# Security View — Evidence Appendix

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Unknown_

## Evidence Appendix

> **Confidence: Unknown** — This appendix surfaces the provider-native evidence and key entities underpinning the Security view. Confidence is marked Unknown at the section level because not all entities carry full analytical context; individual entity confidence ratings are noted inline below.

This appendix is a reference layer for Security & Governance teams. It maps the raw provider identifiers to the friendly names used throughout the Security view, and records the evidence references that ground each finding. Use it to validate claims, raise access requests, or cross-reference with your own tooling.

---

### Entity Reference

The following accounts and resources were identified as key entities for this view. Confidence ratings reflect the quality of provider-derived evidence for each entity.

#### Accounts

| Friendly Name | Account ID | Confidence |
|---|---|---|
| Management Account | `110319895932` | Verified |
| Sandbox Ma Account | `161388682021` | Verified |
| Workload Dev Account | `105769365151` | Verified |
| Workload Prod Account | `122122642149` | Verified |
| Log Archive Account | `122980216815` | Likely |

> **Note (Log Archive Account):** The Log Archive Account (`122980216815`) carries a **Likely** confidence rating — its role as a log archive has been inferred from naming and the presence of the access-logs bucket, but has not been independently confirmed via an authoritative account classification source.

#### Public Subnets (eu-central-1)

Five public subnets were identified across three accounts. Public subnets are directly relevant to exposure analysis.

| Friendly Name | Subnet ID | Account | Confidence |
|---|---|---|---|
| Public Subnet (b065) | `subnet-065f522206524ab12` | Workload Dev (`105769365151`) | Verified |
| Public Subnet (d725) | `subnet-0f64d71c952a7898a` | Workload Dev (`105769365151`) | Verified |
| Public Subnet (5a8a) | `subnet-016c22941a019a137` | Workload Prod (`122122642149`) | Verified |
| Public Subnet (94f8) | `subnet-013a24d318ed6f3d0` | Workload Prod (`122122642149`) | Verified |
| Public Subnet (244e) | `subnet-029e2cceb3d0beff7` | Sandbox Ma (`161388682021`) | Verified |

#### Security Groups

| Friendly Name | Security Group ID | Account | Region | Confidence |
|---|---|---|---|---|
| Cloudox Demo Atlas Dev Sg ECS | `sg-0d6a48061beb72eae` | Workload Dev (`105769365151`) | eu-central-1 | Verified |
| Cloudox Demo Atlas Prod Sg ALB | `sg-06f2b4190bf01d261` | Workload Prod (`122122642149`) | eu-central-1 | Verified |

---

### Evidence

The following provider-native evidence references underpin findings in this view. These identifiers can be used directly in AWS Console, CLI, or audit tooling for independent verification.

#### IAM Roles

| Friendly Name | ARN | Role ID | Account | Confidence |
|---|---|---|---|---|
| cloudox-demo-sandbox-ci-admin | `arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin` | `AROAAAAAAO5VMOEOZ70IX` | Sandbox Ma (`161388682021`) | Verified |
| cloudox-demo-atlas-dev-ingest-sfn | `arn:aws:iam::105769365151:role/cloudox-demo-atlas-dev-ingest-sfn` | `AROAAAAAAIJ1CH5G3USEC` | Workload Dev (`105769365151`) | Verified |
| cloudox-demo-atlas-prod-dr-replicator | `arn:aws:iam::122122642149:role/cloudox-demo-atlas-prod-dr-replicator` | `AROAAAAACFUR6W6I0LR0G` | Workload Prod (`122122642149`) | Verified |

#### Networking Resources

| Resource Type | Friendly Name | ID | Account | Region |
|---|---|---|---|---|
| Elastic IP | cloudox-demo-atlas-dev-nat-eip | `eipalloc-083eada77de5498db` | Workload Dev (`105769365151`) | eu-central-1 |
| Route Table | cloudox-demo-atlas-dev-private-rt | `rtb-009215bffcfd9f9a0` | Workload Dev (`105769365151`) | eu-central-1 |

#### Storage

| Resource Type | Friendly Name | Account | Region |
|---|---|---|---|
| S3 Bucket | `cloudox-demo-access-logs-122980216815-eu-central-1` | Log Archive (`122980216815`) | eu-central-1 |

---

### Changes Since Previous Snapshot

The following security-relevant changes were observed between the previous snapshot (2026-07-20T11:50 UTC) and the current snapshot (2026-07-20T12:54 UTC). All changes listed here are **Observed** (provider-derived).

- **Exposure change — action required:** Security group `sg-0d6a48061beb72eae` (Cloudox Demo Atlas Dev Sg ECS, Workload Dev) **became reachable from the internet** this period. Its `ip_permissions` were also modified. Security & Governance teams should validate whether this internet exposure is intentional and review the updated inbound rules.
- **New IAM roles added (×3):** Three IAM roles were created across accounts — `cloudox-demo-sandbox-ci-admin` (Sandbox Ma, `AROAAAAAAO5VMOEOZ70IX`), `cloudox-demo-atlas-dev-ingest-sfn` (Workload Dev, `AROAAAAAAIJ1CH5G3USEC`), and `cloudox-demo-atlas-prod-dr-replicator` (Workload Prod, `AROAAAAACFUR6W6I0LR0G`). Privilege scope and trust policies for these roles should be reviewed, particularly the CI admin role in the Sandbox account.
- **New Elastic IP allocated:** `eipalloc-083eada77de5498db` (cloudox-demo-atlas-dev-nat-eip) was added in the Workload Dev account (eu-central-1).
- **Route table modified:** `rtb-009215bffcfd9f9a0` (cloudox-demo-atlas-dev-private-rt) had both its associations and routes changed — relevant to understanding current traffic paths from private subnets.
- **Public subnet IP counts decreased:** `subnet-065f522206524ab12` (Dev, 251→249) and `subnet-013a24d318ed6f3d0` (Prod, 250→249) each lost available IPs, indicating new resources were placed in public subnets.
- **Access-logs S3 bucket configuration changed:** `cloudox-demo-access-logs-122980216815-eu-central-1` (Log Archive account) had changes to encryption, lifecycle, public access block, and versioning settings. Governance teams should confirm these changes align with log retention and access control policy.

> **Note:** 13 additional related changes exist beyond those listed here, and `more_in_evolution` is true. See the **Environment Evolution** page for the complete change record.
