# Executive View — Important Unknowns

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Verified_

## Important Unknowns

Four evidence gaps limit the completeness of this view's cost analysis. None block current findings, but each constrains how far leadership can rely on cost attribution and optimization signals today.

### Gaps Leadership Should Know

**~22% of spend is unassociated with discovered architecture.**
Around one-fifth of cloud spend sits in services CloudoX does not yet map to the discovered resource graph. This spend is reported as unassociated rather than force-fit to avoid misleading attribution — but it means cost analysis covers roughly 78% of actual spend at most. Resolving this requires expanding collector coverage for those services.

**Tag-based cost allocation is effectively unavailable.**
Only 1% of resources carry a configured cost-allocation tag. Until tagging coverage improves substantially, it is not possible to reliably slice spend by team, product, or environment using tags alone. CloudoX uses inference for the 781 resources that carry no Environment / Stage / Tier tag, so workload-level cost breakdowns carry inherent uncertainty.

**Right-sizing and idle-resource recommendations are not available.**
CloudoX does not collect CloudWatch utilization metrics in this version. As a result, there are no usage-based signals for identifying idle, underutilized, or over-provisioned resources. Optimization opportunities in this area cannot be quantified until utilization data is collected.

**Several cost drivers for specific services are undetected.**
RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are outside the current collector scope. If the environment uses any of these, their cost contribution is not reflected in architectural cost-driver analysis.
