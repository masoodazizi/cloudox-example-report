# Architect View — Networking & Connectivity

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Verified_

## Networking & Connectivity

Three independent VPCs span a single region (eu-central-1) across three AWS accounts — Dev, Prod, and Sandbox — each with its own internet gateway. The topology is flat and account-isolated: no evidence of VPC peering, Transit Gateway, or shared networking constructs was found within this section's scope. This means inter-environment traffic (e.g., Dev-to-Prod) would traverse the public internet unless connectivity is established elsewhere.

### VPCs & Subnets

The three VPCs and their accounts are:

| Friendly Name | VPC ID | Account | Region |
|---|---|---|---|
| Cloudox Demo Atlas Dev VPC | `vpc-0a4d44a07f48d7ca0` | 105769365151 | eu-central-1 |
| Cloudox Demo Atlas Prod VPC | `vpc-0aaa6d4f2f981e945` | 122122642149 | eu-central-1 |
| Cloudox Demo Sandbox VPC | `vpc-0c97d53850c027a14` | 161388682021 | eu-central-1 |

Five public subnets are confirmed across the three VPCs:

| Friendly Name | Subnet ID | Account |
|---|---|---|
| Public Subnet (b065) | `subnet-065f522206524ab12` | 105769365151 (Dev) |
| Public Subnet (d725) | `subnet-0f64d71c952a7898a` | 105769365151 (Dev) |
| Public Subnet (5a8a) | `subnet-016c22941a019a137` | 122122642149 (Prod) |
| Public Subnet (94f8) | `subnet-013a24d318ed6f3d0` | 122122642149 (Prod) |
| Public Subnet (244e) | `subnet-029e2cceb3d0beff7` | 161388682021 (Sandbox) |

All five confirmed subnets are public-facing. Private subnets, if present, are not represented in this section's package and may be covered elsewhere.

### Routing & Egress

Each VPC has a dedicated internet gateway:

- **Cloudox Demo Atlas Dev Igw** (`igw-0155958a0a5e9c500`) — attached to the Dev VPC (`vpc-0a4d44a07f48d7ca0`)
- **Cloudox Demo Atlas Prod Igw** (`igw-01dd5625ec1ea7a54`) — attached to the Prod VPC (`vpc-0aaa6d4f2f981e945`)
- **Sandbox Internet Gateway** (`igw-08017528fa36ccb4d`) — attached to the Sandbox VPC (`vpc-0c97d53850c027a14`)

A NAT Gateway — **Cloudox Demo Atlas Dev NAT** (`nat-05bf82584b9610324`) — is present in the Dev account (105769365151) in eu-central-1, placed in the public subnet `subnet-065f522206524ab12` (b065). This provides outbound internet access for private resources in the Dev VPC. No equivalent NAT Gateway is evidenced for the Prod or Sandbox VPCs in this section's package.

The presence of NAT in Dev but not (evidenced) in Prod is a design asymmetry worth validating: if Prod hosts private workloads requiring outbound internet access, a NAT Gateway may be absent or covered in a different section.

### Private Connectivity

No evidence of VPC peering connections, AWS Transit Gateway, AWS PrivateLink endpoints, or VPN/Direct Connect attachments was found in this section's package. The three VPCs — Dev, Prod, and Sandbox — appear to be network-isolated from each other. Cross-environment connectivity, if required by workloads, is not accounted for here and should be validated with the environment owner.

Two AWS-managed prefix lists are referenced in the evidence set:
- `pl-66a5400f` (`arn:aws:ec2:eu-central-1:aws:prefix-list/pl-66a5400f`)
- `pl-6ea54007` (`arn:aws:ec2:eu-central-1:aws:prefix-list/pl-6ea54007`)

These are AWS-owned prefix lists (e.g., for S3 or CloudFront gateway endpoints) referenced in security group or route table rules. Their specific usage context is not detailed in this section's package.

### Changes Since Previous Snapshot

Several meaningful networking changes occurred between the previous snapshot (2026-07-20T11:50) and the current one (2026-07-20T12:54):

- **NAT Gateway added:** `nat-05bf82584b9610324` (Cloudox Demo Atlas Dev NAT) was newly provisioned in the Dev VPC — a significant egress architecture change for that environment.
- **Internet exposure reduced:** Security group `sg-04fae132cfc68e91d` is no longer reachable from the internet, indicating a tightening of inbound access rules.
- **ENI placement activity:** Five new ENI-to-subnet relationships were added (ENIs `eni-058ad447b7287912a`, `eni-0dbafb51ea9a9ffd3`, `eni-0fa5e7a1b3798b7d3`, `eni-00815b97162c6a5fc`, `eni-0d070d51a01905b0b` placed into subnets including `subnet-0b0384769d442046a`, `subnet-065f522206524ab12`, `subnet-013a24d318ed6f3d0`, and `subnet-0f64d71c952a7898a`), and one ENI (`eni-0c9de7ff9cd35fe20`) was removed from `subnet-0b0384769d442046a`. This suggests new workloads or services were attached to the network.
- **Available IP counts decreased** in two public subnets: `subnet-065f522206524ab12` (Dev, 251→249) and `subnet-013a24d318ed6f3d0` (Prod, 250→249), consistent with the new ENI placements.

Note: 11 additional related changes exist beyond those listed here. See the Environment Evolution page for the full set.

![Workload VPC network topology](./diagrams/architect-network-topology.png)

> **Figure — Workload VPC network topology.** Which workload VPCs exist, how is each tiered, and what is reachable from the internet? Scope: architect view · networking and connectivity. 2 of 2 workload VPC(s) shown with subnet tiers and evidence-backed connectivity. Default VPCs omitted. Tiers are route-grounded (public = Internet Gateway route or internet-facing load balancer).
