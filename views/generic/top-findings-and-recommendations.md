# Generic View — Top Findings & Recommendations

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Verified_

## Top Findings & Recommendations

Three domains surface actionable signals in this environment: a resilience gap on a production datastore, and cost-pressure patterns across four workloads that warrant owner review before any action is taken. No finding here requires an immediate decision, but the production database risk carries the clearest operational consequence.

> **Overall confidence: Verified** for the architecture finding; **Assumed / Unknown** for cost signals — cost pressure is a prioritization ranking, not an exact spend figure. Validate with resource owners before acting.

---

### Top Findings

#### Single-AZ Production Database

The RDS instance `cloudox-demo-atlas-prod-pg` is deployed without a Multi-AZ standby. A single Availability Zone failure could cause unplanned downtime or data loss for whatever workload depends on it. This is the highest-confidence finding in this section (Verified) and the one with the most direct operational impact.

*Affected resource:* `cloudox-demo-atlas-prod-pg` · *Domain:* Architecture · *Item:* `modernization_opportunity:architecture:cloudox-demo-atlas-prod-pg`

#### Cost Pressure Signals

Four workloads have been ranked as the relatively highest cost-pressure candidates based on architectural cost drivers (resource patterns known to carry charges). These are **prioritization signals, not dollar figures** — exact attribution requires AWS Cost Explorer or CUR.

| Workload | Account | Ranking signal | Architectural cost drivers | Confidence |
|---|---|---|---|---|
| Cloudox (`cloudox`) | 122122642149 (eu-central-1) | 0.80 / 1.0 | 3 | Assumed |
| Cloudox Demo Atlas Dev (`cloudox-demo-atlas-dev`) | 105769365151 (eu-central-1) | 0.23 / 1.0 | 1 | Assumed |
| Cloudox Demo Atlas Dev API (`cloudox-demo-atlas-dev-api`) | 105769365151 (eu-central-1) | 0.18 / 1.0 | 1 | Unknown |
| Cloudox Demo Atlas Prod API (`cloudox-demo-atlas-prod-api`) | 122122642149 (eu-central-1) | 0.18 / 1.0 | 1 | Unknown |

The **Cloudox** workload stands out with the highest ranking signal (0.80/1.0) and three architectural cost drivers; an inferred allocated share of ~41 cost units is associated with it. **Cloudox Demo Atlas Dev** carries an inferred share of ~5. No optimization candidates (e.g. idle or right-sizing opportunities) were detected for any of these workloads — note that CloudoX does not collect CloudWatch utilization metrics in this version, so usage-based right-sizing is not available.

*Items:* `recommendation:cost:cloudox`, `recommendation:cost:cloudox-demo-atlas-dev`, `recommendation:cost:cloudox-demo-atlas-dev-api`, `recommendation:cost:cloudox-demo-atlas-prod-api`

---

### Recommended Actions

1. **Evaluate Multi-AZ for `cloudox-demo-atlas-prod-pg`** — Enable a Multi-AZ standby to reduce the risk of downtime or data loss from a zone failure. This is the only finding here with a clear, low-ambiguity remediation path. (`modernization_opportunity:architecture:cloudox-demo-atlas-prod-pg`)

2. **Review cost drivers for the Cloudox workload with resource owners** — With a ranking signal of 0.80/1.0 and three architectural cost drivers, this workload is the top cost-review priority. Confirm the inferred workload grouping before drawing conclusions. (`recommendation:cost:cloudox`)

3. **Review cost drivers for Atlas Dev and Atlas Dev API** — Both workloads are in account 105769365151 (eu-central-1) and each carry one architectural cost driver. Confidence is Assumed/Unknown; validate groupings with owners first. (`recommendation:cost:cloudox-demo-atlas-dev`, `recommendation:cost:cloudox-demo-atlas-dev-api`)

4. **Review cost drivers for Atlas Prod API** — One architectural cost driver in account 122122642149 (eu-central-1); same validation caveat applies. (`recommendation:cost:cloudox-demo-atlas-prod-api`)

**Material gaps to be aware of before acting on cost signals:**
- ~781 resources rely on inference rather than tags for workload classification; groupings may shift once tagging improves.
- Only 1% of resources carry a cost-allocation tag, making tag-based attribution unreliable today.
- ~22% of spend maps to services not yet covered by CloudoX collectors and is reported as unassociated.
- CloudWatch utilization metrics are not collected in this version, so idle/underutilized resource recommendations are not available.
- RDS read replicas, provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are outside current collector coverage.
