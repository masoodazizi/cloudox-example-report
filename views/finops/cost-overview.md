# FinOps View — Cost Overview

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Likely_

## Cost Overview

**$80.28 in completed-period spend is visible across this AWS organization, with cost concentrating in a small set of workload accounts and architectural patterns — but roughly 22% of that spend cannot yet be tied to discovered architecture, and tag-based allocation is unreliable at 1% tag coverage.**

> **Confidence: Likely.** Figures are derived from graph evidence and AWS Cost Explorer UnblendedCost for complete billing periods. Credits, refunds, and the current partial month are excluded. Some spend remains unassociated and some resource categories are outside current collector scope.

---

### Total Spend Context

Total spend across complete billing periods stands at **$80.28 USD** (UnblendedCost, excluding credits and the current partial month). This figure spans seven accounts in the organization:

| Account | ID | Confidence |
|---|---|---|
| Management Account | 110319895932 | Verified |
| Sandbox Ma Account | 161388682021 | Verified |
| Workload Dev Account | 105769365151 | Verified |
| Workload Prod Account | 122122642149 | Verified |
| Log Archive Account | 122980216815 | Likely |
| Audit Account | 110019496666 | Likely |
| Platform Account | 150982215529 | Likely |

Two material limitations shape how much confidence to place in this total:

- **~22% of spend is unassociated.** CloudoX cannot map it to any discovered architectural resource. It is reported as unassociated rather than force-fit to a workload — meaning the $80.28 figure is complete, but a meaningful slice of it has no architectural explanation yet.
- **Tag coverage is 1%.** Only 1% of resources carry a cost-allocation tag, so any tag-based cost reporting or chargeback in AWS Cost Explorer will be similarly unreliable until tagging is addressed.

These gaps are worth flagging to the environment owner before relying on this data for chargeback or budgeting.

---

### Where Cost Concentrates

CloudoX identified **8 architectural cost drivers** across the environment. The active workload infrastructure is the primary concentration point:

**Workload Dev Account (105769365151)** hosts the `cloudox-demo-atlas-dev` ECS cluster (`arn:aws:ecs:eu-central-1:105769365151:cluster/cloudox-demo-atlas-dev`) running the `cloudox-demo-atlas-dev-api` ECS service (`arn:aws:ecs:eu-central-1:105769365151:service/cloudox-demo-atlas-dev/cloudox-demo-atlas-dev-api`), alongside a Lambda function (`arn:aws:lambda:eu-central-1:105769365151:function:cloudox-demo-atlas-dev-api`) and an API Gateway endpoint (`https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`). Running a full ECS service in a dev account is an architectural decision that carries ongoing compute cost regardless of utilization.

**Workload Prod Account (122122642149)** mirrors this pattern with the `cloudox-demo-atlas-prod-api` ECS service (`arn:aws:ecs:eu-central-1:122122642149:service/cloudox-demo-atlas-prod/cloudox-demo-atlas-prod-api`) and a corresponding Lambda function (`arn:aws:lambda:eu-central-1:122122642149:function:cloudox-demo-atlas-prod-api`). Production workload cost here is expected; the question for FinOps is whether sizing is appropriate — which cannot be confirmed without utilization metrics (see unknowns below).

**Sandbox Ma Account (161388682021)** carries a DynamoDB table (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-scratch`), a Lambda function (`arn:aws:lambda:eu-central-1:161388682021:function:cloudox-demo-sandbox-scratch`), and an API Gateway endpoint (`https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`). Sandbox resources named `scratch` are a candidate for cost review — see the optimization note below.

**1 optimization candidate** has been identified: the sandbox `scratch` stack (DynamoDB table, Lambda, and API Gateway in account 161388682021) warrants validation as to whether it is actively needed. If it is idle or experimental, decommissioning it would eliminate its cost contribution. *This requires validation with the environment owner before action.*

**What CloudoX cannot see from this section's data:**
- Actual utilization metrics (CloudWatch is not collected in this version), so idle or undersized resources cannot be confirmed.
- RDS read replicas, provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are outside current collector scope — cost drivers for those services are not detected here.
- The Log Archive, Audit, and Platform accounts are identified with Likely confidence; their individual cost contributions are not broken out in this section's package.

For precise dollar attribution by service or account, AWS Cost Explorer or CUR remains the authoritative source. CloudoX's value here is connecting spend patterns to the architectural decisions that generate them.
