# FinOps View — Cost Drivers

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Likely_

## Cost Drivers

The environment spans 833 resources across 6 of 7 known accounts and 2 regions (primary: eu-central-1, with a us-east-1 DR footprint). Four significant workloads drive the majority of architectural spend: **Cloudox Demo Atlas Prod API** (Workload Prod Account `122122642149`), **Cloudox Demo Atlas Dev** and **Cloudox Demo Atlas Dev API** (Workload Dev Account `105769365151`), and the **Cloudox** system (also in `122122642149`). A sandbox workload (**Cloudox Demo Sandbox Scratch**, `161388682021`) is also present. Tag coverage is critically low — only ~1% of resources carry a cost-allocation tag — so dollar attribution by workload or team is not yet reliable from tagging alone.

> **Confidence: Likely** — Drivers are derived from graph evidence (resource patterns and relationships). Exact dollar amounts require AWS Cost Explorer / CUR. Spend figures in this section reflect architectural cost patterns, not billed totals.

### Architectural Cost Drivers

**Compute: ECS + Lambda hybrid across prod and dev**
Both the prod and dev workloads run ECS services alongside Lambda functions. The prod API (`arn:aws:ecs:eu-central-1:122122642149:service/cloudox-demo-atlas-prod/cloudox-demo-atlas-prod-api`) and its Lambda counterpart (`arn:aws:lambda:eu-central-1:122122642149:function:cloudox-demo-atlas-prod-api`) represent the primary production compute surface. The dev account mirrors this pattern with its own ECS cluster (`arn:aws:ecs:eu-central-1:105769365151:cluster/cloudox-demo-atlas-dev`), ECS service, and Lambda function. Running parallel ECS + Lambda stacks in both prod and dev means compute costs are effectively doubled at the workload level — a deliberate architectural choice worth validating against actual dev utilization.

**NAT Gateway: a persistent per-hour and data-processing charge in dev**
A NAT Gateway (`nat-05bf82584b9610324`, `arn:aws:ec2:eu-central-1:105769365151:natgateway/nat-05bf82584b9610324`) with an associated Elastic IP (`eipalloc-083eada77de5498db`, named `cloudox-demo-atlas-dev-nat-eip`) is present in the Workload Dev Account. NAT Gateways carry a fixed hourly charge plus a per-GB data-processing fee regardless of traffic volume. In a dev environment this is a candidate for cost reduction — **requires validation** that the dev workload genuinely needs persistent outbound internet access at this scale.

**API Gateway: two public endpoints**
Two API Gateway endpoints are observed: `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com` and `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`. API Gateway charges per request and for data transfer out; cost significance depends on call volume, which is not available in this version (no CloudWatch metrics collected).

**DynamoDB: two tables across sandbox**
Two DynamoDB tables are present in the Sandbox Ma Account (`161388682021`): `cloudox-demo-sandbox-scratch` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-scratch`) and `cloudox-demo-sandbox-events` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-events`). DynamoDB capacity mode (on-demand vs. provisioned) is not captured by current collectors, so the cost profile of these tables cannot be fully assessed here.

**DR footprint in us-east-1 (Workload Prod Account)**
A CloudFormation StackSet (`arn:aws:cloudformation:us-east-1:122122642149:stack/StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536/acb94bf7-b3da-c32a-1613-327a5c08def2`) and a CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`, `arn:aws:cloudwatch:us-east-1:122122642149:alarm:cloudox-demo-atlas-prod-dr-bucket-size`) indicate an active DR configuration in us-east-1. Cross-region replication and standby resources carry ongoing storage and data-transfer costs. The alarm name references a bucket-size metric, suggesting S3 replication is part of this DR pattern.

**Platform and governance services now in scope**
SecurityHub, SSM, and Step Functions have entered the discovered scope. These services carry usage-based charges (Security Hub findings ingestion, SSM parameter/session usage, Step Functions state transitions) that will appear in billing. Their cost weight depends on usage volume not yet measurable here.

**Log Archive Account**
The Log Archive Account (`122980216815`) is in scope. Centralized logging architectures typically accumulate S3 storage costs over time; S3 storage classes are not captured by current collectors, so the cost profile is not assessable here.

### Service & Resource Drivers

| Resource / Service | Account | Region | Cost Pattern | Validation Needed? |
|---|---|---|---|---|
| ECS Service (prod API) | Workload Prod `122122642149` | eu-central-1 | Fargate/EC2 task hours | No — production |
| ECS Service (dev API) | Workload Dev `105769365151` | eu-central-1 | Fargate/EC2 task hours | **Yes** — dev utilization |
| Lambda (prod API) | Workload Prod `122122642149` | eu-central-1 | Invocation + duration | No — production |
| Lambda (dev API) | Workload Dev `105769365151` | eu-central-1 | Invocation + duration | **Yes** — dev utilization |
| Lambda (sandbox scratch) | Sandbox Ma `161388682021` | eu-central-1 | Invocation + duration | **Yes** — sandbox |
| NAT Gateway (dev) | Workload Dev `105769365151` | eu-central-1 | Hourly + per-GB | **Yes** — dev necessity |
| Elastic IP (dev NAT) | Workload Dev `105769365151` | eu-central-1 | Idle EIP charge if unattached | **Yes** — confirm attached |
| API Gateway (×2) | Multiple | eu-central-1 | Per-request + data-out | Volume unknown |
| DynamoDB (sandbox ×2) | Sandbox Ma `161388682021` | eu-central-1 | Capacity mode unknown | **Yes** — capacity mode |
| DR StackSet + S3 alarm | Workload Prod `122122642149` | us-east-1 | Storage + replication | No — intentional DR |
| SecurityHub / SSM / Step Functions | Multiple | — | Usage-based | Monitor going forward |

**Unassociated spend:** Approximately 22% of spend is in services CloudoX does not map to discovered architecture and is reported as unassociated. This portion cannot be attributed to a workload without further investigation in AWS Cost Explorer.

**Tagging gap:** With only ~1% of resources tagged for cost allocation and 781 resources relying on inference for environment classification, tag-based showback or chargeback is not currently viable. Establishing a tagging standard is a prerequisite for reliable cost attribution.

### Changes Since Previous Snapshot

Several cost-relevant resources were added since the previous snapshot (approximately one hour prior):

- **NAT Gateway and Elastic IP added in Workload Dev** (`nat-05bf82584b9610324` / `eipalloc-083eada77de5498db`): These introduce new recurring hourly and data-processing charges in the dev account. This is the most immediate cost-relevant change to validate.
- **DynamoDB table `cloudox-demo-sandbox-events`** added in Sandbox Ma Account — a new table whose capacity mode and expected traffic are unknown.
- **DR CloudFormation StackSet and CloudWatch alarm** added in Workload Prod (us-east-1) — confirms the DR footprint is newly provisioned, not pre-existing.
- **SecurityHub, SSM, and Step Functions** entered discovered scope — usage-based charges for these services will now appear in billing.
- The **Cloudox** workload grew from 9 to 10 member resources, and **Cloudox Demo Atlas Dev** grew from 4 to 5 member resources (inferred grouping changes — treat tentatively).

Note: 98 additional changes were recorded in this snapshot period. See the Environment Evolution page for the full list.
