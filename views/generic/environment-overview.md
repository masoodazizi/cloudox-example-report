# Generic View — Environment Overview

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Likely_

## Environment Overview

This is a multi-account AWS environment operating under the `cloudox-demo` workspace. It spans **6 of 7 known accounts** (1 excluded from scope), holds **833 discovered resources** across **2 observed regions** (eu-central-1 and us-east-1), and runs **4 significant workloads** alongside 1 inferred system. A fifth inferred workload has been demoted to helper/governance status.

**Confidence: Likely** — derived from graph evidence; some classification and coverage gaps remain (see Unknowns below).

---

### Scope & Accounts

Seven accounts are known to CloudoX; six were scanned in this discovery run:

| Friendly Name | Account ID | Confidence |
|---|---|---|
| Management Account | 110319895932 | Verified |
| Workload Dev Account | 105769365151 | Verified |
| Workload Prod Account | 122122642149 | Verified |
| Sandbox Ma Account | 161388682021 | Verified |
| Audit Account | 110019496666 | Likely |
| Platform Account | 150982215529 | Likely |
| Log Archive Account | 122980216815 | Likely |

One account (122980216815 — Log Archive Account) has an unknown environment classification (`122980216815-unknown`); its role is assumed rather than confirmed. The Audit Account (110019496666) and Platform Account (150982215529) are classified as Likely rather than Verified.

Three logical environments are clearly established: a **Development Environment** (Workload Dev Account, 105769365151), a **Production Environment** (Workload Prod Account, 122122642149), and a **Sandbox Environment** (Sandbox Ma Account, 161388682021). A second production-tier environment is associated with the Platform Account (150982215529).

### Regions & Footprint

Two AWS regions are observed in this snapshot:

- **eu-central-1** — primary region; hosts workload resources including API Gateway endpoints (`https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`, `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`), DynamoDB tables (`cloudox-demo-atlas-dev-items` in 105769365151, `cloudox-demo-atlas-prod-items` in 122122642149, `cloudox-demo-sandbox-events` and `cloudox-demo-sandbox-scratch` in 161388682021), and NAT/internet gateway infrastructure across multiple accounts.
- **us-east-1** — secondary region; hosts internet gateways in the Audit Account (110019496666) and Workload Dev Account (105769365151), and a CloudFormation StackSet stack in the Workload Prod Account (`StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536`) that indicates a **disaster-recovery footprint** in this region for production.

Internet connectivity is present: internet gateways are observed in eu-central-1 (105769365151) and us-east-1 (110019496666 and 105769365151), and public internet is referenced as an ingress path. A NAT Gateway (`nat-05bf82584b9610324`, cloudox-demo-atlas-dev-nat) with an associated Elastic IP (`eipalloc-083eada77de5498db`) provides outbound internet access for private subnets in the Development Environment.

> **Coverage note:** Resource Explorer and Cloud Control meta-collectors were unavailable during this scan. Long-tail resource types and full AWS-visible breadth could not be cross-checked; the 833-resource count reflects typed collector coverage only.

### What This Environment Is

This is a **workload-oriented AWS organisation** structured around a conventional landing-zone pattern: a Management Account for organisational governance, dedicated Workload Dev and Workload Prod accounts for application tiers, a Sandbox account for experimentation, and supporting accounts for audit, platform services, and log archiving.

The naming convention (`cloudox-demo-atlas-*`, `cloudox-demo-sandbox-*`) points to at least one primary application — **Atlas** — running across dev and prod tiers, with a sandbox variant for exploratory work. The presence of API Gateway endpoints, DynamoDB tables, Step Functions, SSM, and Security Hub signals a **serverless-leaning, event-driven architecture** with operational tooling (Security Hub for security posture, SSM for systems management) layered on top.

A disaster-recovery configuration for the production Atlas workload is evidenced by the CloudFormation StackSet stack and a CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`) in us-east-1 within the Workload Prod Account.

> **Classification caveat:** 781 of 833 resources carry no Environment/Stage/Tier tag; their workload and environment assignments rely on inference. Treat workload membership counts as indicative rather than authoritative until tagging is confirmed.

---

### Changes Since Previous Snapshot

Between the previous snapshot (2026-07-20T11:50 UTC) and the current one (2026-07-20T12:54 UTC), several additions were observed:

- **Three services entered discovered scope** (Observed): `securityhub`, `ssm`, and `stepfunctions` — these services are now visible to the collector where they were not previously.
- **DR infrastructure added in Workload Prod Account** (Observed): A new CloudFormation StackSet stack (`StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536`) and a CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`) appeared in us-east-1, reinforcing the disaster-recovery footprint for the Atlas production workload.
- **New DynamoDB table in Sandbox** (Observed): `cloudox-demo-sandbox-events` (161388682021, eu-central-1) was added.
- **NAT Gateway and EIP added in Dev** (Observed): `cloudox-demo-atlas-dev-nat` (`nat-05bf82584b9610324`) and its associated Elastic IP (`eipalloc-083eada77de5498db`) appeared in the Workload Dev Account (eu-central-1).
- **Two workloads grew in size** (Inferred): The `cloudox` workload moved from 9 to 10 member resources; `cloudox-demo-atlas-dev` moved from 4 to 5 member resources.

An additional **78 changes** were recorded in this snapshot period but are not enumerated here. See the **Environment Evolution** page for the full change log. Further related changes may be summarised in more specific sections of this view.

![AWS Organizations account structure](./diagrams/generic-org-account-structure.png)

> **Figure — AWS Organizations account structure.** How is this AWS estate organised into accounts and organizational units? Scope: generic view · environment overview. AWS Organizations structure: all 7 account(s) across 4 organizational unit(s), shown in full. Evidence: AWS Organizations account + OU membership.
