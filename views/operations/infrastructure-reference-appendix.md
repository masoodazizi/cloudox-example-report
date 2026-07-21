# Operations View — Infrastructure Reference Appendix

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Unknown_

## Infrastructure Reference Appendix

This appendix provides the canonical friendly-name → raw-ID mapping for the infrastructure entities referenced throughout this Operations view. Use it to correlate console URLs, CLI commands, and alert payloads back to the logical names used elsewhere in this document. Note that the confidence for this section is **Unknown** — the package contains entity references but no accompanying inventory summary items, so completeness cannot be confirmed from this section alone.

### Infrastructure Inventory

The following resource types are represented in this section's evidence set across four AWS accounts, all in **eu-central-1**.

#### Accounts

| Friendly Name | Account ID |
|---|---|
| Management Account | `110319895932` |
| Sandbox Ma Account | `161388682021` |
| Workload Dev Account | `105769365151` |
| Workload Prod Account | `122122642149` |
| Log Archive Account | `122980216815` |

> The Log Archive Account (`122980216815`) is classified as **Likely** rather than Verified — treat it as unconfirmed until validated with the environment owner.

#### NAT Gateway

| Friendly Name | Resource ID | ARN | Account | Region |
|---|---|---|---|---|
| cloudox-demo-atlas-dev-nat | `nat-05bf82584b9610324` | `arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324` | Workload Dev (`105769365151`) | eu-central-1 |

#### ECS Cluster & Service

| Friendly Name | Resource ID / ARN | Account | Region |
|---|---|---|---|
| cloudox-demo-atlas-dev (Cluster) | `arn:aws:ecs:eu-central-1:105769365151:cluster/cloudox-demo-atlas-dev` | Workload Dev (`105769365151`) | eu-central-1 |
| cloudox-demo-atlas-dev-api (Service) | `arn:aws:ecs:eu-central-1:105769365151:service/cloudox-demo-atlas-dev/cloudox-demo-atlas-dev-api` | Workload Dev (`105769365151`) | eu-central-1 |

#### Security Groups

| Friendly Name | Security Group ID | ARN | Account | Region |
|---|---|---|---|---|
| Cloudox Demo Atlas Dev Sg ECS | `sg-0d6a48061beb72eae` | `arn:aws:ec2:eu-central-1:105769365151:security-group/sg-0d6a48061beb72eae` | Workload Dev (`105769365151`) | eu-central-1 |
| Cloudox Demo Atlas Prod Sg ALB | `sg-06f2b4190bf01d261` | — | Workload Prod (`122122642149`) | eu-central-1 |

> **Operational flag:** `sg-0d6a48061beb72eae` (Cloudox Demo Atlas Dev Sg ECS) has recently had its `ip_permissions` modified and is now reachable from the internet. Verify this is intentional before the next change window.

#### Subnets

| Friendly Name | Subnet ID | Account | Region |
|---|---|---|---|
| Public Subnet (b065) | `subnet-065f522206524ab12` | Workload Dev (`105769365151`) | eu-central-1 |
| Public Subnet (d725) | `subnet-0f64d71c952a7898a` | Workload Dev (`105769365151`) | eu-central-1 |
| Public Subnet (5a8a) | `subnet-016c22941a019a137` | Workload Prod (`122122642149`) | eu-central-1 |
| Public Subnet (94f8) | `subnet-013a24d318ed6f3d0` | Workload Prod (`122122642149`) | eu-central-1 |
| Public Subnet (244e) | `subnet-029e2cceb3d0beff7` | Sandbox Ma (`161388682021`) | eu-central-1 |

#### IAM Roles

| Friendly Name | Role ID | ARN | Account |
|---|---|---|---|
| cloudox-demo-sandbox-ci-admin | `AROAAAAAAO5VMOEOZ70IX` | `arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin` | Sandbox Ma (`161388682021`) |
| cloudox-demo-atlas-dev-ingest-sfn | `AROAAAAAAIJ1CH5G3USEC` | `arn:aws:iam::105769365151:role/cloudox-demo-atlas-dev-ingest-sfn` | Workload Dev (`105769365151`) |
| cloudox-demo-atlas-prod-dr-replicator | `AROAAAAACFUR6W6I0LR0G` | `arn:aws:iam::122122642149:role/cloudox-demo-atlas-prod-dr-replicator` | Workload Prod (`122122642149`) |

### Entity Reference

Quick-reference index of all raw identifiers appearing in this section, mapped to their logical names and accounts for use in runbooks, alerts, and CLI queries.

| Raw Identifier | Friendly Name | Type | Account |
|---|---|---|---|
| `110319895932` | Management Account | Account | — |
| `161388682021` | Sandbox Ma Account | Account | — |
| `105769365151` | Workload Dev Account | Account | — |
| `122122642149` | Workload Prod Account | Account | — |
| `122980216815` | Log Archive Account (Likely) | Account | — |
| `nat-05bf82584b9610324` | cloudox-demo-atlas-dev-nat | NAT Gateway | `105769365151` |
| `sg-0d6a48061beb72eae` | Cloudox Demo Atlas Dev Sg ECS | Security Group | `105769365151` |
| `sg-06f2b4190bf01d261` | Cloudox Demo Atlas Prod Sg ALB | Security Group | `122122642149` |
| `subnet-065f522206524ab12` | Public Subnet (b065) | Subnet | `105769365151` |
| `subnet-0f64d71c952a7898a` | Public Subnet (d725) | Subnet | `105769365151` |
| `subnet-016c22941a019a137` | Public Subnet (5a8a) | Subnet | `122122642149` |
| `subnet-013a24d318ed6f3d0` | Public Subnet (94f8) | Subnet | `122122642149` |
| `subnet-029e2cceb3d0beff7` | Public Subnet (244e) | Subnet | `161388682021` |
| `cloudox-demo-atlas-dev` | cloudox-demo-atlas-dev | ECS Cluster | `105769365151` |
| `cloudox-demo-atlas-dev-api` | cloudox-demo-atlas-dev-api | ECS Service | `105769365151` |
| `AROAAAAAAO5VMOEOZ70IX` | cloudox-demo-sandbox-ci-admin | IAM Role | `161388682021` |
| `AROAAAAAAIJ1CH5G3USEC` | cloudox-demo-atlas-dev-ingest-sfn | IAM Role | `105769365151` |
| `AROAAAAACFUR6W6I0LR0G` | cloudox-demo-atlas-prod-dr-replicator | IAM Role | `122122642149` |

### Changes Since Previous Snapshot

The following changes were observed between **2026-07-20T11:50** and **2026-07-20T12:54** UTC and are directly relevant to entities in this appendix:

- **`sg-0d6a48061beb72eae` (Cloudox Demo Atlas Dev Sg ECS)** — `ip_permissions` were modified and the security group became reachable from the internet. This is the highest-priority item to validate. (`sg-0d6a48061beb72eae`, `105769365151|eu-central-1|AWS::EC2::SecurityGroup|arn:aws:ec2:eu-central-1:105769365151:security-group/sg-0d6a48061beb72eae`)
- **`nat-05bf82584b9610324` (cloudox-demo-atlas-dev-nat)** — NAT Gateway was newly added to Workload Dev (`105769365151`) in eu-central-1. (`arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324`)
- **Three IAM roles were added:** `cloudox-demo-sandbox-ci-admin` (Sandbox Ma, `AROAAAAAAO5VMOEOZ70IX`), `cloudox-demo-atlas-dev-ingest-sfn` (Workload Dev, `AROAAAAAAIJ1CH5G3USEC`), and `cloudox-demo-atlas-prod-dr-replicator` (Workload Prod, `AROAAAAACFUR6W6I0LR0G`).
- **`subnet-065f522206524ab12` (Public Subnet b065)** — Available IP count decreased from 251 → 249, consistent with new resource placement. (`subnet-065f522206524ab12`)
- **`subnet-013a24d318ed6f3d0` (Public Subnet 94f8)** — Available IP count decreased from 250 → 249. (`subnet-013a24d318ed6f3d0`)
- **`cloudox-demo-atlas-dev` (ECS Cluster)** — Running task count increased from 1 → 2. (`cloudox-demo-atlas-dev`)
- **`cloudox-demo-atlas-dev-api` (ECS Service)** — Both `desired_count` and `running_count` increased from 1 → 2, indicating a scale-out event. (`cloudox-demo-atlas-dev-api`)

An additional **27 changes** were recorded in this snapshot period but are not enumerated here. See the **Environment Evolution** page for the full change log.
