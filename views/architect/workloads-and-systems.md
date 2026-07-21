# Architect View — Workloads & Systems

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Likely_

## Workloads & Systems

Six distinct workloads and systems have been identified across the `cloudox-demo` workspace, spanning three AWS accounts and anchored primarily in `eu-central-1`. The most architecturally significant grouping is the **Cloudox Demo Atlas** product line, which exists in both a production API workload and a development API workload — each in separate accounts — alongside a cross-account system boundary that aggregates them. A sandbox workload and the core **Cloudox** platform workload round out the picture. Confidence across these groupings is mixed: one workload is Verified, three are Likely, and two are Assumed — meaning architects should treat the Assumed groupings as working hypotheses pending tag or IaC confirmation.

> **Confidence: Likely** — Workload and system boundaries are inferred from graph evidence. 781 resources carry no Environment/Stage/Tier tag and rely on inference for classification; treat structural boundaries as directionally correct but validate before using them as authoritative for design decisions.

---

### Systems

One cross-account system has been identified:

| System | Confidence | Notes |
|---|---|---|
| **Cloudox Demo Atlas Dev** | Assumed | No account or region pinned; inferred as a system boundary grouping the dev-tier Atlas workloads |

The **Cloudox Demo Atlas Dev** system (`Cloudox Demo Atlas Dev`) appears to act as a logical grouping above the individual workloads in the dev account (`105769365151`). Because no account ID or region is directly associated with this system entity, its boundary is assumed rather than observed. Architects should validate whether this system boundary is intentional (e.g., backed by an AWS Organizations OU, a shared tagging scheme, or an explicit IaC construct) or an artefact of inference.

---

### Workloads

Five workloads are identified across three accounts, all with a home region of `eu-central-1`:

| Workload | Account | Confidence | Ref |
|---|---|---|---|
| **Cloudox** | `122122642149` | Verified | `cloudox` |
| **Cloudox Demo Atlas Prod API** | `122122642149` | Likely | `cloudox-demo-atlas-prod-api` |
| **Cloudox Demo Atlas Dev** | `105769365151` | Likely | `cloudox-demo-atlas-dev` |
| **Cloudox Demo Atlas Dev API** | `105769365151` | Likely | `cloudox-demo-atlas-dev-api` |
| **Cloudox Demo Sandbox Scratch** | `161388682021` | Assumed | `cloudox-demo-sandbox-scratch` |

**Cloudox** (`cloudox`, account `122122642149`) is the only Verified workload — its membership is directly evidenced rather than inferred. It currently has 10 member resources (up from 9 in the previous snapshot).

**Cloudox Demo Atlas Prod API** (`cloudox-demo-atlas-prod-api`, account `122122642149`) is the production API surface. Evidence includes the DynamoDB table `cloudox-demo-atlas-prod-items` (`arn:aws:dynamodb:eu-central-1:122122642149:table/cloudox-demo-atlas-prod-items`) and the internet-facing API Gateway endpoint `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`. A DR footprint in `us-east-1` is also associated with this workload (see Components & Tiers below).

**Cloudox Demo Atlas Dev** (`cloudox-demo-atlas-dev`, account `105769365151`) and **Cloudox Demo Atlas Dev API** (`cloudox-demo-atlas-dev-api`, account `105769365151`) represent the development tier. Evidence includes the DynamoDB table `cloudox-demo-atlas-dev-items` (`arn:aws:dynamodb:eu-central-1:105769365151:table/cloudox-demo-atlas-dev-items`), the API Gateway endpoint `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`, and a newly added Elastic IP `eipalloc-083eada77de5498db` (tagged `cloudox-demo-atlas-dev-nat-eip`), suggesting a NAT gateway is present or being introduced in the dev network path. The `cloudox-demo-atlas-dev` workload grew from 4 to 5 member resources in the current snapshot.

**Cloudox Demo Sandbox Scratch** (`cloudox-demo-sandbox-scratch`, account `161388682021`) is an Assumed workload in a dedicated sandbox account. Evidence is limited to two DynamoDB tables: `cloudox-demo-sandbox-events` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-events`) and `cloudox-demo-sandbox-scratch` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-scratch`). The workload boundary and purpose should be confirmed with the owning team.

---

### Components & Tiers

**API tier (internet-facing):** Both the prod and dev Atlas workloads expose API Gateway endpoints directly to the internet (`internet` → `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com` for prod; `internet` → `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com` for dev). This confirms a serverless API front-end pattern in `eu-central-1` for both tiers.

**Data tier:** DynamoDB tables serve as the data store for all three Atlas environments:
- Prod: `cloudox-demo-atlas-prod-items` (account `122122642149`, `eu-central-1`)
- Dev: `cloudox-demo-atlas-dev-items` (account `105769365151`, `eu-central-1`)
- Sandbox: `cloudox-demo-sandbox-events` and `cloudox-demo-sandbox-scratch` (account `161388682021`, `eu-central-1`)

**Networking components:** Internet gateways are present in multiple accounts and regions — `igw-0d14f1dd4e54d5906` (`eu-central-1`, account `110019496666`), `igw-00ed21b9a0e6596a8` (`us-east-1`, account `110019496666`), and `igw-0567575921f471548` (`us-east-1`, account `105769365151`) — indicating VPC-based infrastructure alongside the serverless API layer. The newly added EIP `eipalloc-083eada77de5498db` (`cloudox-demo-atlas-dev-nat-eip`) and network interface `eni-058ad447b7287912a` (account `122122642149`, `eu-central-1`) suggest active VPC egress path changes in the dev and prod accounts respectively.

**DR tier (prod, us-east-1):** A CloudFormation StackSet-deployed stack (`StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536`, account `122122642149`, `us-east-1`) and an associated CloudWatch alarm `cloudox-demo-atlas-prod-dr-bucket-size` indicate a disaster-recovery configuration for the Atlas prod workload in a secondary region. The alarm monitors DR bucket size, suggesting S3-based replication or backup is part of the DR design.

**Orchestration and operations services:** `stepfunctions`, `ssm`, and `securityhub` have entered the discovered scope in this snapshot (see Changes below). Their workload membership is not yet established in this section's package — their architectural role should be confirmed.

---

### Changes Since Previous Snapshot

*Snapshot window: 2026-07-20T11:50 UTC → 2026-07-20T12:54 UTC*

- **Three services entered discovered scope** (Observed): `securityhub`, `ssm`, and `stepfunctions` are now visible. Their workload assignments are not yet resolved in this section.
- **Prod DR stack deployed** (Observed): The CloudFormation stack `StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536` was added in `us-east-1` under account `122122642149`, accompanied by the CloudWatch alarm `cloudox-demo-atlas-prod-dr-bucket-size` — indicating active DR provisioning for the Atlas prod workload.
- **Sandbox events table added** (Observed): `cloudox-demo-sandbox-events` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-events`) was added to the sandbox account.
- **Dev NAT EIP added** (Observed): Elastic IP `eipalloc-083eada77de5498db` (`cloudox-demo-atlas-dev-nat-eip`) was added in account `105769365151`, `eu-central-1`, suggesting a NAT gateway is being introduced or reconfigured in the dev network.
- **New network interface** (Observed): `eni-058ad447b7287912a` was added in account `122122642149`, `eu-central-1`.
- **Workload sizing** (Inferred): The `cloudox` workload grew from 9 to 10 member resources; `cloudox-demo-atlas-dev` grew from 4 to 5 member resources.

Note: 70 additional changes were recorded in this snapshot window but are not enumerated here. See the **Environment Evolution** page for the full change set.

![Workload architecture](./diagrams/architect-workload-architecture.png)

> **Figure — Workload architecture.** What are the significant workloads and how do their tiers connect? Scope: architect view · workloads and systems. 4 of 5 inferred workload(s) shown (most significant first) with ingress → compute → data tiers. Edges are drawn only where a graph relationship exists. 1 additional workload(s) omitted to keep the diagram readable.
