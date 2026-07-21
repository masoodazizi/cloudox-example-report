# Generic View — Cost Signals

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Likely_

## Cost Signals

Total spend captured across complete billing periods stands at **$80.28 USD**, spread across 8 identified architectural cost drivers with 1 active optimization candidate. About 22% of spend falls in services CloudoX cannot yet map to discovered architecture and is reported as unassociated rather than force-fit — so the figures below reflect a partial but grounded picture, not a complete cost breakdown.

### Cost Drivers

The following resource patterns are known to carry charges across the environment's accounts. These are architectural observations, not dollar attributions — exact per-resource cost attribution requires AWS Cost Explorer or CUR.

**Compute (ECS & Lambda)**
- An ECS cluster and API service run in the **Workload Dev Account** (`105769365151`): cluster `cloudox-demo-atlas-dev` hosting service `cloudox-demo-atlas-dev-api`.
- A corresponding ECS API service runs in the **Workload Prod Account** (`122122642149`): `cloudox-demo-atlas-prod-api`.
- Lambda functions are present in three accounts:
  - `cloudox-demo-atlas-dev-api` (Workload Dev, `105769365151`)
  - `cloudox-demo-atlas-prod-api` (Workload Prod, `122122642149`)
  - `cloudox-demo-sandbox-scratch` (Sandbox Ma Account, `161388682021`)

**Data**
- A DynamoDB table (`cloudox-demo-sandbox-scratch`) exists in the **Sandbox Ma Account** (`161388682021`). DynamoDB capacity mode is not captured by current collectors, so whether it is provisioned or on-demand — and the associated cost profile — is not known.

**API Gateway**
- Two API Gateway endpoints are active in `eu-central-1`:
  - `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`
  - `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`
- API Gateway bills per request and per data transfer, so traffic volume drives cost here.

**Networking (NAT Gateway)**
- At least one NAT Gateway (`nat-05bf82584b9610324`) is present in the **Workload Dev Account** (`105769365151`), tagged as non-production. NAT Gateways bill both per hour and per GB processed, making them a persistent baseline cost even at low traffic.

> **Note:** CloudWatch utilization metrics are not collected in this version, so idle or underutilized resource identification and right-sizing recommendations are not available. RDS, Direct Connect, and S3 storage-class cost drivers are also outside current collector scope.

### Optimization Signals

One optimization candidate has been identified.

**NAT Gateway consolidation candidates** (`cost_opportunity:cost:nat-gateway-consolidation`) — *Priority 4, Likely confidence*

1 NAT Gateway carrying non-production tags (`nat-05bf82584b9610324` in Workload Dev Account `105769365151`) is a candidate for consolidation or replacement. NAT Gateways accrue hourly charges regardless of traffic, plus per-GB data processing fees. In non-production environments, options such as shared egress, VPC endpoints for AWS-service traffic, or removal in low-criticality contexts can reduce this cost.

**Recommended action:** Review per-AZ NAT Gateway redundancy against actual availability requirements for the dev environment before acting. Validate egress patterns and HA needs first — this is a candidate, not a confirmed saving.

**Tag coverage gap:** Only ~1% of resources carry a configured cost-allocation tag, which means tag-based cost allocation is not yet reliable across this environment. Improving tagging coverage would significantly increase the accuracy of future cost attribution and optimization analysis.
