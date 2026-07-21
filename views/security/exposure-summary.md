# Security View — Exposure Summary

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Verified_

## Exposure Summary

**Confidence: Verified** — Derived from complete graph evidence for this domain.

Nine internet-facing access paths are active across the environment, spanning one internet-facing load balancer, three security groups with world-open ingress rules, and four public subnets with direct Internet Gateway routes. All findings carry **medium severity** and are Verified from provider-derived evidence. None are flagged as requiring an immediate decision, but each warrants confirmation that the exposure is intentional.

---

### Internet-Reachable Resources

The following resources are directly reachable from the internet or sit in subnets that are:

**Load Balancer**

| Resource | Account | Region | Exposure |
|---|---|---|---|
| `cloudox-demo-atlas-prod-alb` | Unknown | Unknown | Internet-facing scheme |

Account and region details for the load balancer are not available in this section's evidence. Confidence: Unknown for those attributes.

**Security Groups with World-Open Ingress**

| Security Group | Friendly Name | Account | Region | Open Port |
|---|---|---|---|---|
| `sg-0459201826f8de5b3` | Cloudox Demo Atlas Prod Sg Edge | 122122642149 | eu-central-1 | 443 (0.0.0.0/0, ::/0) |
| `sg-06f2b4190bf01d261` | Cloudox Demo Atlas Prod Sg ALB | 122122642149 | eu-central-1 | 80 (0.0.0.0/0, ::/0) |
| `sg-0d6a48061beb72eae` | Cloudox Demo Atlas Dev Sg ECS | 105769365151 | eu-central-1 | 80 (0.0.0.0/0, ::/0) |

Port 443 open to the world on the edge security group (`sg-0459201826f8de5b3`) is consistent with a public-facing HTTPS endpoint, but should be confirmed as intentional. Port 80 open on both the ALB security group (`sg-06f2b4190bf01d261`) and the dev ECS security group (`sg-0d6a48061beb72eae`) warrants review — unencrypted HTTP ingress from the internet is typically used only for redirect purposes and may be unnecessary on the dev ECS tier.

**Public Subnets (Internet Gateway Route)**

| Subnet ID | Friendly Name | Account | Region |
|---|---|---|---|
| `subnet-016c22941a019a137` | cloudox-demo-atlas-prod-public-b | 122122642149 | eu-central-1 |
| `subnet-013a24d318ed6f3d0` | cloudox-demo-atlas-prod-public-a | 122122642149 | eu-central-1 |
| `subnet-0f64d71c952a7898a` | cloudox-demo-atlas-dev-public-b | 105769365151 | eu-central-1 |
| `subnet-065f522206524ab12` | cloudox-demo-atlas-dev-public-a | 105769365151 | eu-central-1 |

All four subnets have route tables forwarding traffic to an Internet Gateway, making any resource placed in them directly internet-reachable (subject to security group rules). Two subnets are in the prod VPC (`vpc-0aaa6d4f2f981e945`, account 122122642149) and two are in the dev VPC (`vpc-0a4d44a07f48d7ca0`, account 105769365151).

---

### Exposure Paths

The environment contains three named VPCs with confirmed internet connectivity, plus several additional VPCs whose purpose and exposure status are not fully characterised in this section's evidence.

**Confirmed internet-connected VPCs:**

| VPC | Friendly Name | Account | Internet Gateway |
|---|---|---|---|
| `vpc-0aaa6d4f2f981e945` | Cloudox Demo Atlas Prod VPC | 122122642149 | `igw-01dd5625ec1ea7a54` |
| `vpc-0a4d44a07f48d7ca0` | Cloudox Demo Atlas Dev VPC | 105769365151 | `igw-0155958a0a5e9c500` |
| `vpc-0c97d53850c027a14` | Cloudox Demo Sandbox VPC | 161388682021 | `igw-08017528fa36ccb4d` |

The Sandbox VPC (`vpc-0c97d53850c027a14`, account 161388682021) has an Internet Gateway attached. No public subnet or security group exposure items were raised for the Sandbox VPC in this section's evidence — however, the presence of an IGW means the path to internet exposure exists at the network layer.

The dev VPC also has a NAT Gateway (`nat-05bf82584b9610324`, `cloudox-demo-atlas-dev-nat`) placed in public subnet `subnet-065f522206524ab12`, providing outbound internet access for private resources in that VPC.

**VPCs with Unknown characterisation (Confidence: Unknown):**

The following VPCs appear in the evidence but lack sufficient metadata to characterise their exposure or purpose in this section:

- `vpc-0fd81e106f24b4e85` — account 161388682021, us-east-1
- `vpc-02298727f33db8908` — account 122980216815, eu-central-1
- `vpc-0038de9730553c915` — account 110019496666, eu-central-1
- `vpc-06bc92d9927a2b9a6` — account 161388682021, eu-central-1
- `vpc-0e0655b83d41e71e6` — account 122122642149, eu-central-1

These should be validated with environment owners to confirm whether they carry internet-facing workloads or are isolated.

---

### Changes Since Previous Snapshot

Between the snapshot at 2026-07-20T11:50 UTC and the current snapshot at 2026-07-20T12:54 UTC, the following exposure-relevant changes were observed:

- **Exposure reduced:** Security group `sg-04fae132cfc68e91d` is no longer reachable from the internet — one previously flagged internet-facing path has been closed.
- **New resource:** NAT Gateway `nat-05bf82584b9610324` (`cloudox-demo-atlas-dev-nat`) was added to account 105769365151 (eu-central-1) and placed in subnet `subnet-065f522206524ab12`. This extends outbound internet routing for the dev VPC's private tier.
- **New services in scope:** `securityhub`, `ssm`, and `stepfunctions` entered the discovered scope — their exposure posture is covered in other sections of this view.
- **Network interface relationships:** Several ENI-to-VPC relationships were added (`eni-058ad447b7287912a`, `eni-0dbafb51ea9a9ffd3`, `eni-0fa5e7a1b3798b7d3` into their respective VPCs) and one was removed (`eni-0c9de7ff9cd35fe20` from `vpc-0aaa6d4f2f981e945`), reflecting network topology changes in the prod and dev VPCs.

Note: 26 additional related changes exist beyond those summarised here. See the Environment Evolution page for the full set.
