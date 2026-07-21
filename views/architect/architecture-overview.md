# Architect View — Architecture Overview

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Likely_

## Architecture Overview

The environment spans **833 resources** across **6 of 7 known accounts** (1 account excluded from scope) and **2 regions** — with eu-central-1 as the primary region and us-east-1 carrying at least a DR footprint. CloudoX identified **4 significant workloads** from 5 inferred, plus **1 cross-account system grouping**, with 1 workload demoted to helper/governance status. The overall confidence for this section is **Likely** — derived from graph evidence, with inference filling gaps where tagging is absent.

> ⚠️ **Tagging gap:** 781 of 833 resources carry no Environment / Stage / Tier tag. Workload and environment boundaries rely on naming-convention inference throughout. Architects should treat groupings as directionally correct but validate boundaries before using them for cost allocation or blast-radius analysis.

### Architectural Shape

The observable shape is a **serverless-first, API-driven architecture** anchored in eu-central-1. Two API Gateway endpoints are present — `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com` and `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com` — both internet-facing, indicating public API surfaces for at least two workloads. DynamoDB tables serve as the primary data stores: `cloudox-demo-atlas-dev-items` (account `105769365151`), `cloudox-demo-atlas-prod-items` (account `122122642149`), `cloudox-demo-sandbox-events`, and `cloudox-demo-sandbox-scratch` (both in account `161388682021`), confirming per-environment table isolation rather than shared data tiers.

Internet gateways are present in both eu-central-1 (accounts `110019496666` and `105769365151`) and us-east-1 (account `110019496666`), indicating VPC-based compute exists alongside the serverless layer — though the specific compute resources are covered in other sections. A NAT EIP (`eipalloc-083eada77de5498db`, tagged `cloudox-demo-atlas-dev-nat-eip`) in account `105769365151` confirms private-subnet egress is configured for the Dev environment. A network interface (`eni-058ad447b7287912a`) in account `122122642149` eu-central-1 is present but its attachment context is not fully resolved in this section.

A CloudFormation StackSet (`StackSet-cloudox-demo-workload-prod-dr-…` in account `122122642149`, us-east-1) signals **infrastructure-as-code-managed DR provisioning** for the Prod workload, reinforced by a CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`) monitoring DR bucket size in the same account and region.

### Environments & Accounts

| Workload | Account | Region | Confidence |
|---|---|---|---|
| Cloudox Demo Atlas Prod API | `122122642149` | eu-central-1 | Likely |
| Cloudox (governance/helper) | `122122642149` | eu-central-1 | Verified |
| Cloudox Demo Atlas Dev | `105769365151` | eu-central-1 | Likely |
| Cloudox Demo Atlas Dev API | `105769365151` | eu-central-1 | Likely |
| Cloudox Demo Sandbox Scratch | `161388682021` | eu-central-1 | Assumed |
| Cloudox Demo Atlas Dev *(system)* | *(cross-account)* | — | Assumed |

The pattern suggests a **three-account environment split**: Prod (`122122642149`), Dev (`105769365151`), and Sandbox (`161388682021`). The Sandbox account hosts two DynamoDB tables (`cloudox-demo-sandbox-events`, `cloudox-demo-sandbox-scratch`) consistent with experimental or event-driven workloads. The Prod account extends into us-east-1 exclusively for DR, not as a second active region. One account (`110019496666`) contributes internet gateways in both regions but its workload assignment is not resolved in this section.

The **Cloudox** workload (`cloudox`, account `122122642149`) is classified as a helper/governance workload — Verified confidence — and is not counted among the 4 significant application workloads.

### Key Patterns

**Serverless API + managed NoSQL:** The combination of API Gateway endpoints and DynamoDB tables across Dev and Prod accounts is the clearest repeating pattern. Each environment maintains its own table (`-dev-items`, `-prod-items`), which is sound for isolation but means schema and capacity management must be replicated per environment.

**IaC-driven DR:** The StackSet deployment in us-east-1 under account `122122642149` — alongside the `cloudox-demo-atlas-prod-dr-bucket-size` alarm — indicates the Prod workload has a structured DR posture managed through CloudFormation StackSets. The scope of that DR stack (which resources it covers) is detailed in the DR/resilience section.

**Emerging service footprint:** Security Hub (`securityhub`), Systems Manager (`ssm`), and Step Functions (`stepfunctions`) all entered discovered scope in the current snapshot. This suggests active expansion of the operational and orchestration surface — architects should confirm whether Step Functions is being introduced as a new orchestration layer within an existing workload or represents a new workload pattern.

**Tagging discipline is a design risk:** With 781 resources relying on inference for classification, any architecture decision that depends on environment boundaries (cost allocation, security policy scoping, blast-radius containment) carries elevated uncertainty until a tagging standard is enforced.

### Changes Since Previous Snapshot

Between the snapshot at 2026-07-20T11:50 UTC and the current snapshot at 2026-07-20T12:54 UTC, several notable changes occurred:

- **Three services entered discovered scope** (Observed): `securityhub`, `ssm`, and `stepfunctions` — indicating either new enablement or first-time discovery coverage of these services.
- **A DR CloudFormation StackSet was added** (Observed) in account `122122642149`, us-east-1: `StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536` (`arn:aws:cloudformation:us-east-1:122122642149:stack/StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536/acb94bf7-b3da-c32a-1613-327a5c08def2`), accompanied by a new CloudWatch alarm `cloudox-demo-atlas-prod-dr-bucket-size` (`arn:aws:cloudwatch:us-east-1:122122642149:alarm:cloudox-demo-atlas-prod-dr-bucket-size`) — consistent with active DR build-out for the Prod workload.
- **A new DynamoDB table** `cloudox-demo-sandbox-events` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-events`) was added in the Sandbox account, suggesting new event-driven activity there.
- **A NAT EIP** `eipalloc-083eada77de5498db` (cloudox-demo-atlas-dev-nat-eip) was added in account `105769365151` eu-central-1, indicating private-subnet egress was newly provisioned for the Dev environment.
- **A network interface** `eni-058ad447b7287912a` (`arn:aws:ec2:eu-central-1:122122642149:network-interface/eni-058ad447b7287912a`) was added in account `122122642149` eu-central-1.
- Two workloads grew in inferred membership (Inferred): `cloudox` from 9 to 10 resources, and `cloudox-demo-atlas-dev` from 4 to 5 resources.

Note: 77 additional changes occurred in this snapshot period and are not enumerated here — see the **Environment Evolution** page for the full picture.

![AWS Organizations account structure](./diagrams/architect-org-account-structure.png)

> **Figure — AWS Organizations account structure.** How is this AWS estate organised into accounts and organizational units? Scope: architect view · architecture overview. AWS Organizations structure: all 7 account(s) across 4 organizational unit(s), shown in full. Evidence: AWS Organizations account + OU membership.
