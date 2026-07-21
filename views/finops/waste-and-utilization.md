# FinOps View — Waste / Utilization Signals

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Likely_

## Waste / Utilization Signals

_What the evidence says (and cannot say) about waste and utilization._

_Covers: Idle & Unused Resources · Utilization Signals_

**Assumptions**

- Spend figures use the AWS Cost Explorer UnblendedCost metric for complete billing periods only; the current (partial) month is excluded. Credits and refunds are excluded, so figures reflect actual usage cost (as in the AWS Cost Explorer console's default view), not the net amount invoiced after promotional or free-tier credits.
- Architectural cost drivers describe resource patterns known to carry charges; they are not dollar attributions. Exact cost attribution comes from AWS Cost Explorer / CUR.

**Unknowns**

- CloudoX does not collect CloudWatch utilization metrics in this version; idle, underutilized, or right-sizing recommendations based on actual usage are not available.
- RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are not captured by the current collectors, so cost drivers for them are not detected.
- About 22% of spend is in services CloudoX does not map to discovered architecture; it is reported as unassociated rather than force-fit.
- Only 1% of resources carry a configured cost-allocation tag, so tag-based cost allocation is not yet reliable.
