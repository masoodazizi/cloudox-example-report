# Architect View — Technical Reference Appendix

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Unknown_

## Technical Reference Appendix

This appendix provides friendly-name-to-raw-identifier mappings and evidence references for all entities cited across the Architect view. Use it to cross-reference resources when working with the AWS Console, CLI, or IaC tooling.

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
| Public Subnet (b065) | `subnet-065f522206524ab12` | Workload Dev (`105769365151`) | eu-central-1 | Verified |
| Public Subnet (d725) | `subnet-0f64d71c952a7898a` | Workload Dev (`105769365151`) | eu-central-1 | Verified |
| Public Subnet (5a8a) | `subnet-016c22941a019a137` | Workload Prod (`122122642149`) | eu-central-1 | Verified |
| Public Subnet (94f8) | `subnet-013a24d318ed6f3d0` | Workload Prod (`122122642149`) | eu-central-1 | Verified |
| Public Subnet (244e) | `subnet-029e2cceb3d0beff7` | Sandbox Ma (`161388682021`) | eu-central-1 | Verified |

#### Security Groups

| Friendly Name | Security Group ID | Account | Region | Confidence |
|---|---|---|---|---|
| Cloudox Demo Atlas Dev Sg ECS | `sg-0d6a48061beb72eae` | Workload Dev (`105769365151`) | eu-central-1 | Verified |
| Cloudox Demo Atlas Prod Sg ALB | `sg-06f2b4190bf01d261` | Workload Prod (`122122642149`) | eu-central-1 | Verified |

### Evidence

The following raw evidence references underpin the entities and changes recorded in this view snapshot (run at `2026-07-20T12:54:55Z`).

| Evidence Reference | Description |
|---|---|
| `sg-0d6a48061beb72eae` | Security group — Cloudox Demo Atlas Dev Sg ECS (Workload Dev, eu-central-1) |
| `105769365151\|eu-central-1\|AWS::EC2::SecurityGroup\|arn:aws:ec2:eu-central-1:105769365151:security-group/sg-0d6a48061beb72eae` | CloudFormation-style resource key for the ECS security group |
| `arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324` | NAT Gateway — cloudox-demo-atlas-dev-nat (Workload Dev, eu-central-1) |
| `nat-05bf82584b9610324` | NAT Gateway resource ID |
| `105769365151\|eu-central-1\|AWS::EC2::NatGateway\|arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324` | CloudFormation-style resource key for the NAT Gateway |
| `arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin` | IAM Role — cloudox-demo-sandbox-ci-admin (Sandbox Ma Account) |
| `AROAAAAAAO5VMOEOZ70IX` | Role unique ID for cloudox-demo-sandbox-ci-admin |
| `161388682021\|-\|AWS::IAM::Role\|arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin` | CloudFormation-style resource key for the sandbox CI admin role |
| `arn:aws:iam::105769365151:role/cloudox-demo-atlas-dev-ingest-sfn` | IAM Role — cloudox-demo-atlas-dev-ingest-sfn (Workload Dev Account) |
| `AROAAAAAAIJ1CH5G3USEC` | Role unique ID for cloudox-demo-atlas-dev-ingest-sfn |
| `105769365151\|-\|AWS::IAM::Role\|arn:aws:iam::105769365151:role/cloudox-demo-atlas-dev-ingest-sfn` | CloudFormation-style resource key for the dev ingest Step Functions role |
| `arn:aws:iam::122122642149:role/cloudox-demo-atlas-prod-dr-replicator` | IAM Role — cloudox-demo-atlas-prod-dr-replicator (Workload Prod Account) |
| `AROAAAAACFUR6W6I0LR0G` | Role unique ID for cloudox-demo-atlas-prod-dr-replicator |
| `122122642149\|-\|AWS::IAM::Role\|arn:aws:iam::122122642149:role/cloudox-demo-atlas-prod-dr-replicator` | CloudFormation-style resource key for the prod DR replicator role |
| `105769365151\|eu-central-1\|AWS::EC2::Subnet\|arn:aws:ec2:eu-central-1:105769365151:subnet/subnet-065f522206524ab12` | CloudFormation-style resource key for Public Subnet (b065) |
| `subnet-065f522206524ab12` | Subnet resource ID — Public Subnet (b065), Workload Dev |
| `122122642149\|eu-central-1\|AWS::EC2::Subnet\|arn:aws:ec2:eu-central-1:122122642149:subnet/subnet-013a24d318ed6f3d0` | CloudFormation-style resource key for Public Subnet (94f8) |
| `subnet-013a24d318ed6f3d0` | Subnet resource ID — Public Subnet (94f8), Workload Prod |
| `internet` | Logical reference representing internet exposure source |
| `eni-0dbafb51ea9a9ffd3` | Elastic Network Interface placed into Public Subnet (b065) |

### Changes Since Previous Snapshot

The following changes were observed between `2026-07-20T11:50:27Z` and `2026-07-20T12:54:55Z` and are relevant to entities referenced in this appendix.

- **Security group `sg-0d6a48061beb72eae` (Cloudox Demo Atlas Dev Sg ECS)** had its inbound rules modified (`ip_permissions` changed) and became reachable from the internet — a new `exposed_to_internet` relationship was recorded. This is the most architecturally significant change in this snapshot and warrants review in the Security Architecture section.
- **NAT Gateway `nat-05bf82584b9610324` (cloudox-demo-atlas-dev-nat)** was added to Workload Dev (`105769365151`) in eu-central-1 (`arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324`).
- **Three IAM roles were added:**
  - `cloudox-demo-sandbox-ci-admin` (`AROAAAAAAO5VMOEOZ70IX`) in Sandbox Ma Account (`161388682021`).
  - `cloudox-demo-atlas-dev-ingest-sfn` (`AROAAAAAAIJ1CH5G3USEC`) in Workload Dev Account (`105769365151`).
  - `cloudox-demo-atlas-prod-dr-replicator` (`AROAAAAACFUR6W6I0LR0G`) in Workload Prod Account (`122122642149`).
- **Public Subnet (b065)** (`subnet-065f522206524ab12`, Workload Dev) lost 2 available IP addresses (251 → 249), consistent with the new ENI `eni-0dbafb51ea9a9ffd3` being placed into it.
- **Public Subnet (94f8)** (`subnet-013a24d318ed6f3d0`, Workload Prod) lost 1 available IP address (250 → 249).

An additional 17 related changes exist beyond those listed here. See the **Environment Evolution** page for the complete record.
