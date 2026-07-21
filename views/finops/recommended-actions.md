# FinOps View — Recommended Actions

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Likely_

## Recommended Actions

### Recommended Cost Actions

Four workloads have been identified as prioritized cost-review candidates. These are ranked by architectural cost pressure — a signal derived from the number and weight of cost-driving resource patterns detected, not a direct dollar figure. No action should be taken without first validating findings with resource owners.

> **Important framing:** Cost pressure rankings are prioritization signals, not cost attributions. Exact spend figures require AWS Cost Explorer or CUR. All four recommendations carry Assumed or Unknown confidence, meaning they are grounded in architectural evidence but not yet confirmed against billing data.

| Priority | Workload | Account | Ranking Signal | Architectural Cost Drivers | Optimization Candidates | Inferred Allocated Share |
|---|---|---|---|---|---|---|
| 1 | **Cloudox** (`cloudox`) | 122122642149 | 0.80 / 1.0 | 3 | 0 | ~41 (relative units) |
| 2 | **Cloudox Demo Atlas Dev** (`cloudox-demo-atlas-dev`) | 105769365151 | 0.23 / 1.0 | 1 | 0 | ~5 (relative units) |
| 3 | **Cloudox Demo Atlas Dev API** (`cloudox-demo-atlas-dev-api`) | 105769365151 | 0.18 / 1.0 | 1 | 0 | Not available |
| 3 | **Cloudox Demo Atlas Prod API** (`cloudox-demo-atlas-prod-api`) | 122122642149 | 0.18 / 1.0 | 1 | 0 | Not available |

**Cloudox** (`cloudox`, account `122122642149`) carries the highest cost pressure by a significant margin — a ranking signal of 0.80/1.0 driven by 3 detected architectural cost drivers, with an inferred allocated share of ~41 relative units. This workload is the most important starting point for a cost conversation with resource owners. Confidence: Assumed — validate against Cost Explorer before acting.

**Cloudox Demo Atlas Dev** (`cloudox-demo-atlas-dev`, account `105769365151`) ranks second with a ranking signal of 0.23/1.0 and 1 architectural cost driver. Its inferred allocated share of ~5 relative units is substantially lower than Cloudox, suggesting it is a secondary priority. Confidence: Assumed.

**Cloudox Demo Atlas Dev API** (`cloudox-demo-atlas-dev-api`, account `105769365151`) and **Cloudox Demo Atlas Prod API** (`cloudox-demo-atlas-prod-api`, account `122122642149`) share equal ranking signals of 0.18/1.0, each with 1 architectural cost driver and no inferred allocated share available. Both carry Unknown confidence — they are flagged for review but require the most validation before drawing conclusions. Note that the prod API workload (`cloudox-demo-atlas-prod-api`) warrants attention given its production context, even at the same signal level as its dev counterpart.

No optimization candidates (e.g. idle or right-sizing opportunities) were detected across any of these workloads. This is a known gap: CloudoX does not collect CloudWatch utilization metrics in this version, so usage-based right-sizing recommendations are not available. Optimization signals may emerge once utilization data is incorporated.

**Material gaps to be aware of before acting:**
- Approximately 22% of spend maps to services not associated with discovered architecture and is not reflected in these workload rankings.
- Only 1% of resources carry a cost-allocation tag, making tag-based attribution unreliable at this time.
- 781 resources have no Environment/Stage/Tier tag and rely on inference for workload classification, which affects ranking accuracy.
- Cost drivers for RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are not detected by current collectors.

**Suggested next steps (requiring validation):**
1. Bring the Cloudox workload (`cloudox`, account `122122642149`) to a cost review with its resource owners — its 3 architectural cost drivers and dominant ranking signal make it the highest-value starting point.
2. Cross-reference all four workloads against AWS Cost Explorer using the `eu-central-1` region and the respective account IDs to confirm actual spend before prioritizing remediation.
3. Initiate a cost-allocation tagging effort — at 1% tag coverage, workload-level cost attribution is not yet reliable enough to support chargeback or showback.
