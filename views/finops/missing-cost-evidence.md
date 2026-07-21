# FinOps View — Missing Cost Evidence

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Likely_

## Missing Cost Evidence

Before acting on the cost picture in this view, FinOps teams should understand where the evidence stops. Several gaps limit how far cost can be attributed to workloads, how much spend is explained, and whether utilization-based optimization is possible today. These are not findings about the environment — they are boundaries on what CloudoX can currently see.

### Cost Data Gaps

**~22% of spend is unassociated with discovered architecture.**
About 22% of total spend falls in services that CloudoX does not map to any discovered workload or resource. Rather than force-fitting this spend to an architectural component, it is reported as unassociated. This means the cost breakdowns elsewhere in this view cover roughly 78% of spend at most; the remaining portion requires direct investigation in AWS Cost Explorer or CUR to identify its source. (`evidence_gap:cost:about-22-of-spend-is-in-services-cloudox-does-not-map-to-discovered-architecture-it-is-reported-as-unassociated-rather-than-force-fit`)

**Tag-based cost allocation is not yet reliable.**
Only 1% of resources carry a configured cost-allocation tag. This makes workload- or team-level cost attribution via tags effectively unusable at present. Any chargeback or showback model that relies on tags will be incomplete until tagging coverage is substantially improved. (`evidence_gap:cost:only-1-of-resources-carry-a-configured-cost-allocation-tag-so-tag-based-cost-allocation-is-not-yet-reliable`)

**No utilization metrics — right-sizing is not possible from this data.**
CloudoX does not collect CloudWatch utilization metrics in this version. As a result, there are no idle, underutilized, or right-sizing recommendations grounded in actual usage data. Optimization candidates identified elsewhere in this view are based on architectural patterns and configuration, not measured load. (`evidence_gap:cost:cloudox-does-not-collect-cloudwatch-utilization-metrics-in-this-version-idle-underutilized-or-right-sizing-recommendations-based-on-actual-usage-are-not-available`)

**Several cost-significant resource attributes are not captured.**
The current collectors do not capture RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, or S3 storage classes. Cost drivers associated with these configurations are therefore not detected or surfaced in this view. If any of these services are material to the bill, their cost patterns will not appear here. (`evidence_gap:cost:rds-read-replicas-rds-provisioned-iops-dynamodb-capacity-mode-direct-connect-and-s3-storage-classes-are-not-captured-by-the-current-collectors-so-cost-drivers-for-them-are-not-detected`)

**Discovery breadth could not be cross-checked.**
Both the Cloud Control meta-collector and Resource Explorer meta-collector were disabled or unavailable during discovery. This means long-tail resource types are limited to typed collector coverage, and the full AWS-visible resource breadth could not be independently verified. There may be resource types — and associated costs — that fall outside what CloudoX discovered. (`evidence_gap:coverage:cloud-control-meta-collector-was-disabled-long-tail-resource-types-are-limited-to-typed-collector-coverage`, `evidence_gap:coverage:resource-explorer-meta-collector-was-disabled-or-unavailable-aws-visible-breadth-could-not-be-cross-checked`)

### What to Enable

The following actions would close the most impactful gaps for FinOps purposes. Each requires validation with the environment owner before acting.

| Gap | What to enable or fix | Expected benefit |
|---|---|---|
| 22% unassociated spend | Investigate unassociated services directly in AWS Cost Explorer / CUR | Bring full spend into scope for architectural attribution |
| 1% tag coverage | Implement and enforce a cost-allocation tagging strategy across accounts | Enable reliable workload- and team-level chargeback/showback |
| No utilization data | Enable CloudWatch metric collection in a future CloudoX version or supplement with AWS Compute Optimizer | Unlock evidence-based right-sizing and idle resource identification |
| Missing resource attributes | Enable typed collectors for RDS, DynamoDB, Direct Connect, and S3 storage class detail | Surface cost drivers for these services in future views |
| Discovery breadth | Re-enable Cloud Control and Resource Explorer meta-collectors | Cross-check that no resource types are missing from the cost picture |

> **Confidence: Likely** — The gaps described here are derived from collector configuration and graph evidence. The 22% unassociated spend figure and 1% tag coverage figure are package-derived; exact dollar amounts require AWS Cost Explorer or CUR for confirmation.
