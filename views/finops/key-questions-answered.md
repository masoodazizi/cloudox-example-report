# FinOps View — Key Questions Answered

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Unknown_

## Key Questions Answered

### What is driving the cost of this environment?

The environment carries **$80.28 in observed spend** across 833 resources in 6 accounts, with **8 architectural cost drivers** identified across the workload landscape. The two highest-pressure workloads are **Cloudox** (Workload Prod Account `122122642149`, ranking signal 0.80/1.0, inferred allocated share ~41) and **Cloudox Demo Atlas Dev** (Workload Dev Account `105769365151`, ranking signal 0.23/1.0, inferred allocated share ~5). Cost pressure in both cases stems from architectural decisions — ECS services, Lambda functions, API Gateway endpoints, and a NAT Gateway — rather than from a single dominant line item. The inferred allocated shares are prioritization signals, not exact cost figures, and should be validated against billing data.

Confidence: Assumed — cost pressure rankings are inferred from architectural signals; exact workload-level billing is not available in this package.

### Where is spend concentrated by account, region, and service?

All observed resources are in **eu-central-1**. Spend-bearing accounts identified in this package are the **Workload Prod Account** (`122122642149`) and **Workload Dev Account** (`105769365151`), which host the primary application workloads (ECS clusters and services, Lambda functions, API Gateway endpoints, and a NAT Gateway). The **Sandbox Ma Account** (`161388682021`) carries additional Lambda and DynamoDB resources. Exact per-account or per-service cost breakdowns are not available in this section's package — the $80.28 figure is the total observed spend across the environment.

Confidence: Likely — account and region attribution is evidence-grounded; service-level cost split is not available here.

### Where is there likely waste or low utilization?

The clearest candidate is the **NAT Gateway** (`nat-05bf82584b9610324`) in the Workload Dev Account (`105769365151`). NAT Gateways bill both per hour and per GB of data processed; this gateway carries non-production tags, making it a consolidation or removal candidate. Replacing it with VPC endpoints, a shared egress model, or removing it in low-criticality environments could reduce cost — but this **requires validation** of egress patterns and HA requirements before any action is taken.

Beyond the NAT Gateway, the **Cloudox Demo Atlas Dev** workload (`cloudox-demo-atlas-dev`) in the dev account shows cost pressure from 1 architectural driver with an inferred allocated share of ~5. Dev-environment resources running at production-equivalent scale are a common source of avoidable spend and are worth reviewing with resource owners.

Confidence: Likely for the NAT Gateway candidate; Assumed for the dev workload cost pressure.

### What optimization opportunities are worth validating?

Two items are flagged as requiring validation before any action:

| Opportunity | Account | Signal | Action Required |
|---|---|---|---|
| NAT Gateway consolidation (`nat-05bf82584b9610324`) | Workload Dev (`105769365151`) | Non-production tagged; per-hour + per-GB billing | Validate egress needs and HA requirements; consider VPC endpoints or shared egress |
| Cloudox workload cost review (`cloudox`) | Workload Prod (`122122642149`) | Highest cost-pressure ranking (0.80/1.0); 3 architectural drivers | Review cost drivers with resource owners before acting |
| Cloudox Demo Atlas Dev cost review (`cloudox-demo-atlas-dev`) | Workload Dev (`105769365151`) | 1 architectural driver; dev environment | Review whether dev resources are right-sized |

All three are **prioritization signals, not confirmed savings figures**. Each requires owner validation before any cost action is taken.

Confidence: Assumed — opportunities are inferred from architectural signals, not from billing analysis.

### What cost or commitment risks exist?

No commitment-level risks (e.g. Reserved Instance gaps, Savings Plan coverage) are present in this section's package. However, two architectural risks carry indirect cost implications:

- **Single-AZ database** (`cloudox-demo-atlas-prod-pg`) in the production environment has no Multi-AZ standby. A zone failure could cause downtime or data loss — enabling Multi-AZ would increase cost but reduce recovery risk. This is an availability trade-off requiring a deliberate decision.
- **Broadly-privileged IAM roles** (`cloudox-demo-sandbox-ci-admin`, `cloudox-demo-sandbox-unused-admin`) in the Sandbox Ma Account (`161388682021`) carry a potential over-privilege blast radius. While not a direct cost risk, a security incident stemming from over-privileged identities can have significant cost consequences. These are inferred from naming conventions and must be validated.

Confidence: Verified for the single-AZ finding; Assumed for the IAM role privilege breadth.

### What cost evidence is missing and should be enabled?

This section's package does not include granular billing data (per-service, per-resource cost breakdowns) or utilization metrics. The $80.28 total spend figure is available, but workload-level cost attribution relies on inferred allocation signals rather than tagged billing data. To improve cost visibility:

- **Cost allocation tags** should be validated and enforced across all accounts to enable workload-level billing attribution.
- **AWS Cost Explorer** or equivalent tooling should be confirmed as active and accessible for the accounts in scope — no evidence of this is present in this package.
- **GuardDuty and IAM Access Analyzer** are missing from 5 of 6 scanned accounts (`122980216815`, `105769365151`, `122122642149`, `110319895932`, `161388682021`). While these are security controls, their absence is a governance gap that also limits the completeness of the environment picture used for cost analysis.

Confidence: Unknown — billing tooling enablement status is not confirmed in this package.
