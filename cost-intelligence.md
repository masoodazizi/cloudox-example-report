# Cost Intelligence

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> CloudoX explains cloud cost in **architectural context** — it does not replace AWS Cost Explorer, the Cost & Usage Report, or a dedicated FinOps platform.

## Executive cost summary

- Most recent complete billing period: **2026-06-01 → 2026-07-01**
- Total cost: **80.28 USD**
- Month-over-month: ▲ 41.62 USD (+107.6%) vs 2026-05-01
- Largest services: Amazon Elastic Load Balancing (24.2%), Amazon Relational Database Service (20.7%), Amazon Virtual Private Cloud (17.9%)

## Cost trend

| Period | Cost |
|---|---|
| 2026-01-01 -> 2026-02-01 | 0.00 USD |
| 2026-02-01 -> 2026-03-01 | 0.00 USD |
| 2026-03-01 -> 2026-04-01 | 0.00 USD |
| 2026-04-01 -> 2026-05-01 | 0.00 USD |
| 2026-05-01 -> 2026-06-01 | 38.67 USD |
| 2026-06-01 -> 2026-07-01 | 80.28 USD |

## Top cost drivers

Largest spend by service (from AWS Cost Explorer):

| Service | Cost | Share |
|---|---|---|
| Amazon Elastic Load Balancing | 19.44 USD | 24.2% |
| Amazon Relational Database Service | 16.62 USD | 20.7% |
| Amazon Virtual Private Cloud | 14.34 USD | 17.9% |
| Amazon Elastic Container Service | 10.68 USD | 13.3% |
| AmazonCloudWatch | 8.40 USD | 10.5% |
| AWS Key Management Service | 3.99 USD | 5.0% |
| Claude Sonnet 4.6 (Amazon Bedrock Edition) | 3.31 USD | 4.1% |
| AWS Secrets Manager | 1.60 USD | 2.0% |
| Amazon Simple Storage Service | 1.33 USD | 1.6% |
| Amazon Route 53 | 0.50 USD | 0.6% |

Architectural patterns that carry charges (from the knowledge graph; counts, not dollar attributions):

| Pattern | Count | Category | Basis |
|---|---|---|---|
| Multi-region resource footprint | 2 | networking | Inferred |
| NAT Gateways | 1 | networking | Observed |
| ECS services | 2 | compute | Observed |
| Lambda functions | 3 | compute | Observed |
| Application/Network Load Balancers | 1 | compute | Observed |
| S3 buckets | 10 | storage | Observed |
| DynamoDB tables | 4 | database | Observed |
| RDS instances | 1 | database | Observed |

## Cost pressure by workload

**Cost pressure** is a deterministic *ranking signal* (0-1) blending associated spend, architectural cost drivers, optimization candidates, workload significance, and exposure. It is a prioritization hint, **not a cost figure**. Any associated spend is an inferred allocated share, never exact workload cost.

| Workload | Class | Pressure | Associated spend | Drivers | Candidates |
|---|---|---|---|---|---|
| cloudox | business | ####. 0.80 | 41.40 USD (inferred allocated share (not exact cost)) | 3 | 0 |
| cloudox-demo-atlas-dev | business | #.... 0.23 | 5.34 USD (inferred allocated share (not exact cost)) | 1 | 0 |
| cloudox-demo-atlas-dev-api | business | #.... 0.18 | — | 1 | 0 |
| cloudox-demo-atlas-prod-api | business | #.... 0.18 | — | 1 | 0 |
| cloudox-demo-sandbox-scratch _(demoted)_ | helper | #.... 0.11 | — | 1 | 0 |

## Cost pressure by environment

| Environment | Class | Pressure | Associated spend | Workloads |
|---|---|---|---|---|
| 122122642149-production | production | ####. 0.85 | 41.40 USD (inferred allocated share (not exact cost)) | 2 |
| 105769365151-development | development | #.... 0.28 | 5.34 USD (inferred allocated share (not exact cost)) | 2 |
| 161388682021-sandbox | sandbox | #.... 0.11 | — | 1 |

## Cost pressure by system

| System | Pressure | Associated spend | Workloads |
|---|---|---|---|
| Cloudox Demo Atlas Dev | #.... 0.28 | 5.34 USD (inferred allocated share (not exact cost)) | 2 |

## Spend associations

Each row links a Cost Explorer spend line to the discovered architecture. The **basis** and **confidence** state how the join was made — a service total is exact, but the architectural mapping is inferred and is never exact per-resource attribution.

| Spend line | Amount | Share | Category | Basis | Confidence |
|---|---|---|---|---|---|
| Amazon Elastic Load Balancing | 19.44 USD | 24.2% | networking | inferred_service_association | Likely |
| Amazon Relational Database Service | 16.62 USD | 20.7% | database | inferred_service_association | Likely |
| Amazon Virtual Private Cloud | 14.34 USD | 17.9% | networking | inferred_service_association | Assumed |
| Amazon Elastic Container Service | 10.68 USD | 13.3% | compute | inferred_service_association | Likely |
| Amazon Simple Storage Service | 1.33 USD | 1.6% | storage | inferred_service_association | Likely |
| Amazon DynamoDB | 0.00 USD | 0.0% | database | inferred_service_association | Likely |

**Unassociated spend:** 17.87 USD (22.3% of total) is in services CloudoX does not map to discovered architecture. It is reported as unassociated rather than force-fit to a workload.

## Spend by account

| Key | Cost | Share |
|---|---|---|
| 122122642149 | 66.74 USD | 83.1% |
| 105769365151 | 6.44 USD | 8.0% |
| 150982215529 | 3.99 USD | 5.0% |
| 122980216815 | 1.71 USD | 2.1% |
| 110019496666 | 1.00 USD | 1.2% |
| 161388682021 | 0.40 USD | 0.5% |
| 110319895932 | 0.00 USD | 0.0% |

## Spend by region

| Key | Cost | Share |
|---|---|---|
| eu-central-1 | 78.78 USD | 98.1% |
| us-east-1 | 1.00 USD | 1.2% |
| global | 0.50 USD | 0.6% |

## Spend by service

| Key | Cost | Share |
|---|---|---|
| Amazon Elastic Load Balancing | 19.44 USD | 24.2% |
| Amazon Relational Database Service | 16.62 USD | 20.7% |
| Amazon Virtual Private Cloud | 14.34 USD | 17.9% |
| Amazon Elastic Container Service | 10.68 USD | 13.3% |
| AmazonCloudWatch | 8.40 USD | 10.5% |
| AWS Key Management Service | 3.99 USD | 5.0% |
| Claude Sonnet 4.6 (Amazon Bedrock Edition) | 3.31 USD | 4.1% |
| AWS Secrets Manager | 1.60 USD | 2.0% |
| Amazon Simple Storage Service | 1.33 USD | 1.6% |
| Amazon Route 53 | 0.50 USD | 0.6% |
| Amazon EC2 Container Registry (ECR) | 0.06 USD | 0.1% |
| Amazon API Gateway | 0.00 USD | 0.0% |
| Amazon GuardDuty | 0.00 USD | 0.0% |
| Amazon DynamoDB | 0.00 USD | 0.0% |

## Spend by usage type

| Key | Cost | Share |
|---|---|---|
| EUC1-LoadBalancerUsage | 19.44 USD | 24.2% |
| EUC1-PublicIPv4:InUseAddress | 14.34 USD | 17.9% |
| EUC1-InstanceUsage:db.t4g.micro | 13.68 USD | 17.0% |
| EUC1-CW:MetricMonitorUsage | 8.40 USD | 10.5% |
| EUC1-Fargate-ARM-vCPU-Hours:perCPU | 6.71 USD | 8.3% |
| eu-central-1-KMS-Keys | 3.00 USD | 3.7% |
| EUC1-RDS:GP3-Storage | 2.74 USD | 3.4% |
| EUC1-SpotUsage-Fargate-ARM-vCPU-Hours:perCPU | 2.05 USD | 2.5% |
| EUC1-MP:EUC1_OutputTokenCount-Units | 1.78 USD | 2.2% |
| EUC1-AWSSecretsManager-Secrets | 1.60 USD | 2.0% |
| EUC1-MP:EUC1_InputTokenCount-Units | 1.53 USD | 1.9% |
| EUC1-Fargate-ARM-GB-Hours | 1.47 USD | 1.8% |
| EUC1-Requests-Tier1 | 1.04 USD | 1.3% |
| us-east-1-KMS-Keys | 0.99 USD | 1.2% |
| HostedZone | 0.50 USD | 0.6% |
| EUC1-SpotUsage-Fargate-ARM-GB-Hours | 0.45 USD | 0.6% |
| EUC1-TimedStorage-ByteHrs | 0.31 USD | 0.4% |
| EUC1-RDS:ChargedBackupUsage | 0.20 USD | 0.2% |
| EUC1-Requests-Tier2 | 0.04 USD | 0.1% |
| EUC1-LCUUsage | 0.00 USD | 0.0% |
| EUC1-ApiGatewayHttpRequest | 0.00 USD | 0.0% |
| EUC1-PaidEventsAnalyzed | 0.00 USD | 0.0% |
| Requests-Tier1 | 0.00 USD | 0.0% |
| EUC1-PublicIPv4:IdleAddress | 0.00 USD | 0.0% |
| EUC1-AWSSecretsManagerAPIRequest | 0.00 USD | 0.0% |

## Architectural cost drivers

### Multi-region resource footprint (Inferred)

Count: 2 · Category: networking · Regions: eu-central-1, us-east-1

Resources span multiple regions; inter-region replication and data transfer typically carry per-GB charges. Review whether cross-region traffic is intentional.

### NAT Gateways (Observed)

Count: 1 · Category: networking · Accounts: 1 · Regions: eu-central-1

NAT Gateways bill per hour and per GB processed; data-transfer charges scale with egress through them.

Examples: `nat-05bf82584b9610324`

### ECS services (Observed)

Count: 2 · Category: compute · Accounts: 2 · Regions: eu-central-1

ECS services on Fargate bill per vCPU/GB-hour per running task.

Examples: `cloudox-demo-atlas-dev-api`, `cloudox-demo-atlas-prod-api`

### Lambda functions (Observed)

Count: 3 · Category: compute · Accounts: 3 · Regions: eu-central-1

Lambda bills per request and per GB-second; cost depends on invocation volume, which CloudoX does not measure.

Examples: `cloudox-demo-atlas-dev-api`, `cloudox-demo-atlas-prod-api`, `cloudox-demo-sandbox-scratch`

### Application/Network Load Balancers (Observed)

Count: 1 · Category: compute · Accounts: 1 · Regions: eu-central-1

ELBv2 load balancers bill per hour plus an LCU/NLCU usage dimension.

Examples: `cloudox-demo-atlas-prod-alb`

### S3 buckets (Observed)

Count: 10 · Category: storage · Accounts: 5 · Regions: eu-central-1, us-east-1

S3 bills per GB-month by storage class plus request and data-transfer charges; stored volume is not captured by CloudoX.

Examples: `cloudox-cust-sandbox-a8d4`, `cloudox-demo-access-logs-122980216815-eu-central-1`, `cloudox-demo-atlas-dev-app-105769365151-eu-central-1`, `cloudox-demo-atlas-prod-app-122122642149-eu-central-1`, `cloudox-demo-atlas-prod-dr-snapshots-122122642149-us-east-1`, `cloudox-demo-cloudtrail-122980216815-eu-central-1`, `cloudox-demo-config-122980216815-eu-central-1`, `cloudox-demo-sandbox-161388682021-eu-central-1`, `cloudox-demo-sandbox-abandoned-161388682021-eu-central-1`, `cloudox-public-example`

### DynamoDB tables (Observed)

Count: 4 · Category: database · Accounts: 3 · Regions: eu-central-1

DynamoDB bills by capacity mode (provisioned or on-demand) plus storage; capacity mode is not captured by CloudoX.

Examples: `cloudox-demo-atlas-dev-items`, `cloudox-demo-atlas-prod-items`, `cloudox-demo-sandbox-events`, `cloudox-demo-sandbox-scratch`

### RDS instances (Observed)

Count: 1 · Category: database · Accounts: 1 · Regions: eu-central-1

RDS instances bill per instance-hour by class. Top classes: db.t4g.small x1.

Examples: `cloudox-demo-atlas-prod-pg`

## Optimization opportunities

Each item is a **candidate requiring validation**, not confirmed waste. CloudoX does not collect utilization metrics, so these are structural signals only.

### NAT Gateway consolidation candidates

Confidence: medium · Category: networking

NAT Gateways bill per hour and per GB. 1 NAT Gateway(s) carry non-production tags. Consolidating or replacing them (e.g. shared egress, VPC endpoints, or removing them in low-criticality environments) can reduce cost.

**Recommended next step:** Review per-AZ NAT Gateway redundancy against availability requirements, especially in non-production environments. Requires validation of egress and HA needs.

Affected resources (sample): `nat-05bf82584b9610324`

## Cost-allocation tag coverage

Graph-derived coverage of cost-allocation tag keys (not Cost Explorer tag spend): how much of the discovered footprint carries an allocation tag, so a reader knows whether tag-based cost allocation is feasible.

- Overall: **52/10000** resources (**1%**) carry a configured cost-allocation tag.

| Tag key | Tagged | Coverage |
|---|---|---|
| Application | 0 | 0% |
| BusinessUnit | 0 | 0% |
| CostCenter | 1 | 0% |
| Environment | 52 | 1% |
| Owner | 1 | 0% |
| Project | 52 | 1% |
| Team | 0 | 0% |
| app | 0 | 0% |
| application | 0 | 0% |
| cost-center | 0 | 0% |
| env | 0 | 0% |
| environment | 0 | 0% |
| owner | 0 | 0% |
| project | 0 | 0% |
| team | 0 | 0% |

## Assumptions

- Spend figures use the AWS Cost Explorer UnblendedCost metric for complete billing periods only; the current (partial) month is excluded. Credits and refunds are excluded, so figures reflect actual usage cost (as in the AWS Cost Explorer console's default view), not the net amount invoiced after promotional or free-tier credits.
- Architectural cost drivers describe resource patterns known to carry charges; they are not dollar attributions. Exact cost attribution comes from AWS Cost Explorer / CUR.

## Limitations and missing permissions

- CloudoX does not collect CloudWatch utilization metrics in this version; idle, underutilized, or right-sizing recommendations based on actual usage are not available.
- RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are not captured by the current collectors, so cost drivers for them are not detected.
- About 22% of spend is in services CloudoX does not map to discovered architecture; it is reported as unassociated rather than force-fit.
- Only 1% of resources carry a configured cost-allocation tag, so tag-based cost allocation is not yet reliable.

## Cost questions evidence cannot yet answer

An honest cost answer states what it cannot know. Each gap below is a cost question the currently collected evidence cannot answer, and why.

### Cost-allocation tag coverage is low (warning)

Only 1% of resources carry a configured cost-allocation tag, so tag-based cost allocation is not yet reliable.

Affected questions:
- Can spend be allocated to teams/projects?

**To close this gap:** Adopt and enforce a cost-allocation tagging standard, then activate the tags in the AWS billing console.

### No utilization metrics collected (info)

CloudoX does not collect CloudWatch / Compute Optimizer metrics in this version, so right-sizing and idle/underutilized claims cannot be made — only structural optimization candidates.

Affected questions:
- Which resources are idle or oversized?

**To close this gap:** Add an opt-in utilization adapter (Phase 3) to assert utilization-based right-sizing.

### A material share of spend is unassociated (info)

About 22% of spend is in services CloudoX does not map to discovered architecture; it is reported as unassociated rather than force-fit.

Affected questions:
- Which architectural decisions drive cost?

**To close this gap:** Review the unmapped services directly in Cost Explorer.

## Suggested next steps

- Validate the optimization candidates above with resource owners before acting.
- Correlate the largest spend services with the architectural cost drivers to confirm where cost concentrates.
- Re-run after the next discovery to compare cost intelligence run-over-run as Environment Evolution support matures.
