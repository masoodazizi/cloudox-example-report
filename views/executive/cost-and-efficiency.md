# Executive View — Cost & Efficiency Signals

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Likely_

## Cost & Efficiency Signals

The environment spans four active accounts — **Workload Dev**, **Workload Prod**, **Sandbox Ma**, and **Management** — plus **Log Archive** and **Audit** accounts. One actionable cost reduction candidate has been identified. However, several material gaps limit full cost visibility: roughly 22% of spend cannot be mapped to discovered architecture, and only 1% of resources carry cost-allocation tags, making tag-based attribution unreliable today.

### Where the Money Goes

Exact spend figures are not available in this section's package — detailed cost attribution should be reviewed directly in AWS Cost Explorer or CUR. What is known architecturally is that the environment runs active ECS services across both Dev (`cloudox-demo-atlas-dev-api`) and Prod (`cloudox-demo-atlas-prod-api`) accounts in `eu-central-1`, alongside API Gateway endpoints and a DynamoDB table in the Sandbox account. These resource patterns are known cost carriers (compute, data transfer, API calls, and storage), but dollar amounts are not stated here.

Three coverage gaps are worth flagging for leadership:
- **~22% of spend is unassociated** — it exists in billing but cannot be mapped to any discovered resource. This is reported as-is rather than force-attributed.
- **Tag coverage is 1%** — cost allocation by team, environment, or product is not currently actionable.
- **CloudWatch utilization metrics are not collected** in this version, so idle or over-provisioned resource recommendations are not available.

### Efficiency Opportunities

One cost reduction candidate has been identified: **NAT Gateway consolidation** (`cost_opportunity:cost:nat-gateway-consolidation`).

| Candidate | Account | Detail |
|---|---|---|
| Cloudox Demo Atlas Dev NAT Gateway | Workload Dev | Non-production tagged; billed per hour + per GB egress |

NAT Gateways accrue charges continuously regardless of traffic volume. One NAT Gateway carrying non-production tags has been flagged as a consolidation candidate. Options to evaluate include shared egress, replacing with VPC endpoints for AWS-service traffic, or removing the gateway in low-criticality environments where high availability is not required.

**This is a candidate, not a confirmed saving** — validate egress patterns and HA requirements before acting. No immediate leadership decision is required, but delegating a validation review to the platform or infrastructure team is recommended.

> **Confidence: Likely.** The opportunity is derived from graph evidence of resource presence and tagging; actual cost impact requires validation against billing data in AWS Cost Explorer.
