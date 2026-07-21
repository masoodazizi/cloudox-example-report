# Architect View — Modernization Opportunities

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Likely_

## Modernization Opportunities

The most actionable architectural gap identified is a single-AZ production database that carries real availability risk. Beyond that, cost-pressure signals across four workloads flag areas worth a structured review — though these are prioritization signals, not precise cost attributions. Total spend in scope is **$80.28** across 8 architectural cost drivers and 1 optimization candidate.

> **Confidence: Likely** — Derived from graph evidence; some unknowns remain (see below).

### Modernization Candidates

#### Single-AZ Production Datastore — `cloudox-demo-atlas-prod-pg`

The RDS instance **cloudox-demo-atlas-prod-pg** (account `122122642149`, `eu-central-1`) runs without a Multi-AZ standby. A single Availability Zone failure would cause unplanned downtime and potential data loss for the production workload it serves. This is the only architecture-domain finding in this section and carries **Verified** confidence.

**Recommended action:** Evaluate enabling a Multi-AZ standby replica. The trade-off is a roughly 2× instance cost for the standby, offset against the recovery posture improvement. Confirm RTO/RPO requirements with the workload owner before acting.

#### Cost-Pressure Workloads

Four workloads surface as cost-review candidates based on architectural cost-driver ranking. These are **prioritization signals only** — not exact cost figures — and confidence on the workload-to-cost mapping is Assumed or Unknown for all four. Validate with resource owners before taking any cost action.

| Workload | Friendly Name | Account | Ranking Signal | Cost Drivers | Confidence |
|---|---|---|---|---|---|
| `cloudox` | Cloudox | `122122642149` | 0.80 / 1.0 | 3 | Assumed |
| `cloudox-demo-atlas-dev` | Cloudox Demo Atlas Dev | `105769365151` | 0.23 / 1.0 | 1 | Assumed |
| `cloudox-demo-atlas-dev-api` | Cloudox Demo Atlas Dev API | `105769365151` | 0.18 / 1.0 | 1 | Unknown |
| `cloudox-demo-atlas-prod-api` | Cloudox Demo Atlas Prod API | `122122642149` | 0.18 / 1.0 | 1 | Unknown |

The **Cloudox** workload (`cloudox`) carries the highest relative cost pressure (ranking signal 0.80/1.0) with 3 architectural cost drivers identified. An inferred allocated share of ~41 spend units is associated with it. The two dev workloads (`cloudox-demo-atlas-dev` and `cloudox-demo-atlas-dev-api`) together account for a smaller share (~5 inferred units for the dev workload; no allocation figure available for the dev API workload).

> **Note on cost figures:** Spend figures use AWS Cost Explorer UnblendedCost for complete billing periods; credits and free-tier amounts are excluded. Architectural cost drivers describe resource patterns known to carry charges — they are not dollar attributions. Exact attribution requires AWS Cost Explorer or CUR analysis.

### Recommended Improvements

1. **Enable Multi-AZ on `cloudox-demo-atlas-prod-pg`** — This is the only Verified architectural modernization opportunity in this section. Prioritize it for the production environment given the availability impact of a zone failure.

2. **Conduct a cost-driver review for the `cloudox` workload** — With 3 architectural cost drivers and the highest ranking signal (0.80), this workload is the strongest candidate for a structured cost review. Engage resource owners in account `122122642149` to identify which drivers are intentional vs. addressable.

3. **Assess dev environment sizing** — Both `cloudox-demo-atlas-dev` and `cloudox-demo-atlas-dev-api` (account `105769365151`) show cost-driver signals. Dev environments are common candidates for right-sizing, scheduled shutdown, or consolidation — but validate actual usage patterns first, as CloudoX does not currently collect CloudWatch utilization metrics.

4. **Improve tagging coverage** — Only ~1% of resources carry a configured cost-allocation tag, and ~781 resources rely on inference for environment/stage classification. Improving tag coverage will make future cost attribution and workload boundary analysis significantly more reliable.

> **Material unknowns affecting this section:**
> - CloudWatch utilization metrics are not collected in this version; idle/underutilized resource recommendations are not available.
> - RDS read replicas, provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are not captured by current collectors — cost drivers for these are not detected.
> - ~22% of spend maps to services not yet linked to discovered architecture and is reported as unassociated.
> - Tag-based cost allocation is unreliable at current coverage levels.
