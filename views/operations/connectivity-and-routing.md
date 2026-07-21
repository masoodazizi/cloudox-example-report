# Operations View — Connectivity & Routing

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Verified_

## Connectivity & Routing

The environment spans **15 VPCs** and **63 subnets** across three AWS accounts in `eu-central-1`, with **9 internet-facing access paths** observed and a single load balancer. Three distinct VPCs anchor the topology: the Atlas Dev VPC (`vpc-0a4d44a07f48d7ca0`), the Atlas Prod VPC (`vpc-0aaa6d4f2f981e945`), and the Sandbox VPC (`vpc-0c97d53850c027a14`). Each has a dedicated Internet Gateway, and the Atlas Dev environment now also has a NAT Gateway — added in the most recent snapshot.

### Network Connectivity

Three key VPCs are present across three accounts:

| Friendly Name | VPC ID | Account | IGW |
|---|---|---|---|
| Cloudox Demo Atlas Dev VPC | `vpc-0a4d44a07f48d7ca0` | 105769365151 | `igw-0155958a0a5e9c500` |
| Cloudox Demo Atlas Prod VPC | `vpc-0aaa6d4f2f981e945` | 122122642149 | `igw-01dd5625ec1ea7a54` |
| Cloudox Demo Sandbox VPC | `vpc-0c97d53850c027a14` | 161388682021 | `igw-08017528fa36ccb4d` |

All three VPCs have Internet Gateways attached, making them capable of hosting internet-facing resources. The environment summary reports 9 internet-facing access paths — operators should confirm that all 9 are intentional and that security group rules appropriately restrict exposure. The security group `sg-04fae132cfc68e91d` is no longer internet-reachable (see Changes section), which reduces the internet-facing surface.

Public subnets identified across accounts:

| Friendly Name | Subnet ID | Account |
|---|---|---|
| Public Subnet (b065) | `subnet-065f522206524ab12` | 105769365151 |
| Public Subnet (d725) | `subnet-0f64d71c952a7898a` | 105769365151 |
| Public Subnet (5a8a) | `subnet-016c22941a019a137` | 122122642149 |
| Public Subnet (94f8) | `subnet-013a24d318ed6f3d0` | 122122642149 |
| Public Subnet (244e) | `subnet-029e2cceb3d0beff7` | 161388682021 |

Multiple ENIs were placed into or removed from subnets in the latest snapshot (see Changes), indicating active workload movement — operators should verify these placements are expected and that subnet IP capacity is not approaching exhaustion.

### Routing & Gateways

Each of the three primary VPCs has a dedicated Internet Gateway:

- **Atlas Dev IGW** (`igw-0155958a0a5e9c500`) — attached to Cloudox Demo Atlas Dev VPC (`vpc-0a4d44a07f48d7ca0`), account 105769365151.
- **Atlas Prod IGW** (`igw-01dd5625ec1ea7a54`) — attached to Cloudox Demo Atlas Prod VPC (`vpc-0aaa6d4f2f981e945`), account 122122642149.
- **Sandbox IGW** (`igw-08017528fa36ccb4d`) — attached to Cloudox Demo Sandbox VPC (`vpc-0c97d53850c027a14`), account 161388682021.

The NAT Gateway **Cloudox Demo Atlas Dev NAT** (`nat-05bf82584b9610324`) is present in account 105769365151, `eu-central-1`. It was newly added in the current snapshot. This enables private subnet resources in the Atlas Dev VPC to reach the internet without being directly exposed. Operational note: NAT Gateways are a single-AZ resource — if the AZ hosting `nat-05bf82584b9610324` becomes unavailable, private subnet egress in Atlas Dev will be disrupted unless a secondary NAT Gateway exists in another AZ. No evidence of a redundant NAT Gateway is present in this section's package.

### Egress Paths

Two egress patterns are in use:

1. **Direct internet egress via IGW** — available from public subnets in all three VPCs. Resources in public subnets with appropriate route table entries and security groups can reach and be reached from the internet directly.
2. **NAT-mediated egress** — available in the Atlas Dev VPC via `nat-05bf82584b9610324` (`cloudox-demo-atlas-dev-nat`), enabling private subnet resources to initiate outbound connections without inbound exposure.

AWS-managed prefix lists `pl-66a5400f` and `pl-6ea54007` are referenced in the evidence set, indicating that at least one security group or route table references AWS service prefix lists (e.g., for S3 or DynamoDB gateway endpoints). This is consistent with controlled egress to AWS services, but the specific services and consuming resources are not detailed in this section's package.

Operational risk: With 9 internet-facing access paths across 15 VPCs, any misconfigured route table or overly permissive security group in a public subnet could inadvertently expose private workloads. Continuous review of route table associations and security group rules for public subnets is warranted.

### Changes Since Previous Snapshot

Between `2026-07-20T11:50` and `2026-07-20T12:54` (UTC), the following connectivity changes were observed:

- **NAT Gateway added**: `nat-05bf82584b9610324` (`cloudox-demo-atlas-dev-nat`) was created in account 105769365151. This is a significant topology change — private subnet egress in Atlas Dev is now routed through this gateway. Verify route tables have been updated to direct private subnet traffic through it. [`arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324`]
- **Security group exposure reduced**: `sg-04fae132cfc68e91d` is no longer reachable from the internet. Confirm this is intentional and that dependent services have not lost required inbound connectivity.
- **Subnet IP counts decreased**: `subnet-065f522206524ab12` (cloudox-demo-atlas-dev-public-a) dropped from 251 → 249 available IPs; `subnet-013a24d318ed6f3d0` (cloudox-demo-atlas-prod-public-a) dropped from 250 → 249. This is consistent with the new ENI placements below.
- **ENI placements added**: Five new ENI-to-subnet relationships were added — `eni-0dbafb51ea9a9ffd3` and `eni-00815b97162c6a5fc` into `subnet-065f522206524ab12`; `eni-0fa5e7a1b3798b7d3` into `subnet-013a24d318ed6f3d0`; `eni-058ad447b7287912a` into `subnet-0b0384769d442046a`; `eni-0d070d51a01905b0b` into `subnet-0f64d71c952a7898a`.
- **ENI placement removed**: `eni-0c9de7ff9cd35fe20` was removed from `subnet-0b0384769d442046a`.

An additional **11 related changes** were truncated from this summary. Further detail is available on the Environment Evolution page.

![Workload VPC network topology](./diagrams/operations-network-topology.png)

> **Figure — Workload VPC network topology.** Which workload VPCs exist, how is each tiered, and what is reachable from the internet? Scope: operations view · connectivity and routing. 2 of 2 workload VPC(s) shown with subnet tiers and evidence-backed connectivity. Default VPCs omitted. Tiers are route-grounded (public = Internet Gateway route or internet-facing load balancer).
