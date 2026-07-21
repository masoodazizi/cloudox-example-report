# Generic View — Reference Appendix

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Unknown_

## Reference Appendix

This appendix maps every friendly name used throughout the view to its raw cloud identifier, and records the evidence references that underpin the snapshot. Use it to cross-check ARNs, resource IDs, and account numbers when working directly in the AWS console or CLI.

### Entity Reference

#### Accounts

| Friendly Name | Account ID | Confidence |
|---|---|---|
| Management Account | `110319895932` | Verified |
| Sandbox Ma Account | `161388682021` | Verified |
| Workload Dev Account | `105769365151` | Verified |
| Workload Prod Account | `122122642149` | Verified |
| Log Archive Account | `122980216815` | Likely |

#### Subnets

| Friendly Name | Subnet ID | Account | Region | Confidence |
|---|---|---|---|---|
| Public Subnet (eu-central-1, 105769365151, b065) | `subnet-065f522206524ab12` | Workload Dev (`105769365151`) | eu-central-1 | Verified |
| Public Subnet (eu-central-1, 105769365151, d725) | `subnet-0f64d71c952a7898a` | Workload Dev (`105769365151`) | eu-central-1 | Verified |
| Public Subnet (eu-central-1, 122122642149, 5a8a) | `subnet-016c22941a019a137` | Workload Prod (`122122642149`) | eu-central-1 | Verified |
| Public Subnet (eu-central-1, 122122642149, 94f8) | `subnet-013a24d318ed6f3d0` | Workload Prod (`122122642149`) | eu-central-1 | Verified |
| Public Subnet (eu-central-1, 161388682021, 244e) | `subnet-029e2cceb3d0beff7` | Sandbox Ma (`161388682021`) | eu-central-1 | Verified |

#### Security Groups

| Friendly Name | Security Group ID | Account | Region | Confidence |
|---|---|---|---|---|
| Cloudox Demo Atlas Dev Sg ECS | `sg-0d6a48061beb72eae` | Workload Dev (`105769365151`) | eu-central-1 | Verified |
| Cloudox Demo Atlas Prod Sg ALB | `sg-06f2b4190bf01d261` | Workload Prod (`122122642149`) | eu-central-1 | Verified |

### Evidence

The following raw identifiers and ARNs are the evidence references for this section.

#### Security Groups

- `sg-0d6a48061beb72eae` — `arn:aws:ec2:eu-central-1:105769365151:security-group/sg-0d6a48061beb72eae` (Cloudox Demo Atlas Dev Sg ECS, Workload Dev, eu-central-1)

#### NAT Gateways

- `nat-05bf82584b9610324` — `arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324` (cloudox-demo-atlas-dev-nat, Workload Dev, eu-central-1)

#### IAM Roles

| Friendly Name | Role ID | ARN |
|---|---|---|
| cloudox-demo-sandbox-ci-admin | `AROAAAAAAO5VMOEOZ70IX` | `arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin` |
| cloudox-demo-atlas-dev-ingest-sfn | `AROAAAAAAIJ1CH5G3USEC` | `arn:aws:iam::105769365151:role/cloudox-demo-atlas-dev-ingest-sfn` |
| cloudox-demo-atlas-prod-dr-replicator | `AROAAAAACFUR6W6I0LR0G` | `arn:aws:iam::122122642149:role/cloudox-demo-atlas-prod-dr-replicator` |

#### Subnets (Evidence)

- `subnet-065f522206524ab12` — `arn:aws:ec2:eu-central-1:105769365151:subnet/subnet-065f522206524ab12`
- `subnet-013a24d318ed6f3d0` — `arn:aws:ec2:eu-central-1:122122642149:subnet/subnet-013a24d318ed6f3d0`

#### Network Interfaces

- `eni-0dbafb51ea9a9ffd3` (referenced in subnet relationship; see Changes below)

---

> **Confidence — Unknown.** This appendix is a reference index; confidence applies to individual entities as noted in the tables above. The Log Archive Account (`122980216815`) carries a **Likely** confidence — treat its account ID as provisional until confirmed by the environment owner.

### Changes Since Previous Snapshot

Between the snapshots at **11:50 UTC** and **12:54 UTC on 2026-07-20**, the following changes were observed for entities listed in this appendix:

- **New resources added:**
  - NAT Gateway `nat-05bf82584b9610324` (`cloudox-demo-atlas-dev-nat`) was created in Workload Dev (`105769365151`), eu-central-1. (`arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324`)
  - IAM Role `cloudox-demo-sandbox-ci-admin` (`AROAAAAAAO5VMOEOZ70IX`) was added to Sandbox Ma Account (`161388682021`). (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin`)
  - IAM Role `cloudox-demo-atlas-dev-ingest-sfn` (`AROAAAAAAIJ1CH5G3USEC`) was added to Workload Dev (`105769365151`). (`arn:aws:iam::105769365151:role/cloudox-demo-atlas-dev-ingest-sfn`)
  - IAM Role `cloudox-demo-atlas-prod-dr-replicator` (`AROAAAAACFUR6W6I0LR0G`) was added to Workload Prod (`122122642149`). (`arn:aws:iam::122122642149:role/cloudox-demo-atlas-prod-dr-replicator`)

- **Modified resources:**
  - Security group `sg-0d6a48061beb72eae` (`cloudox-demo-atlas-dev-sg-ecs`) had its inbound rules (`ip_permissions`) changed, and became reachable from the internet — a new `exposed_to_internet` relationship was recorded. (`105769365151|eu-central-1|AWS::EC2::SecurityGroup|arn:aws:ec2:eu-central-1:105769365151:security-group/sg-0d6a48061beb72eae`)
  - Subnet `subnet-065f522206524ab12` (`cloudox-demo-atlas-dev-public-a`) available IP count decreased from 251 → 249; a new ENI (`eni-0dbafb51ea9a9ffd3`) was placed into it. (`105769365151|eu-central-1|AWS::EC2::Subnet|arn:aws:ec2:eu-central-1:105769365151:subnet/subnet-065f522206524ab12`)
  - Subnet `subnet-013a24d318ed6f3d0` (`cloudox-demo-atlas-prod-public-a`) available IP count decreased from 250 → 249. (`122122642149|eu-central-1|AWS::EC2::Subnet|arn:aws:ec2:eu-central-1:122122642149:subnet/subnet-013a24d318ed6f3d0`)

An additional **17 related changes** were recorded but are not enumerated here. See the **Environment Evolution** page for the full list.
