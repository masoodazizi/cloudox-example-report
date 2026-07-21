# Operations View — Monitoring & Logging Gaps

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Likely_

## Monitoring & Logging Gaps

The most operationally significant finding here is not what is monitored, but what cannot be confirmed: two meta-collectors that would validate the completeness of discovery were both disabled or unavailable during this run, meaning the 833 resources captured may not represent the full environment. Treat all gap assessments below as lower bounds.

### Logging Coverage

A CloudTrail org-level trail (`cloudox-demo-org-trail-o-aaaapzvebq`) is present and has an associated logging role (`cloudox-demo-org-trail-logs`). This is the primary evidence of centralised API-level audit logging across the organisation.

However, two structural gaps limit confidence in coverage completeness:

- **Resource Explorer meta-collector was disabled or unavailable** — AWS-visible resource breadth could not be cross-checked against what CloudoX discovered. It is not possible to confirm that all accounts and regions with active resources are covered by the trail or by any other logging mechanism. (`evidence_gap:coverage:resource-explorer-meta-collector-was-disabled-or-unavailable-aws-visible-breadth-could-not-be-cross-checked`)
- **Cloud Control meta-collector was disabled** — Long-tail resource types (those not covered by typed collectors) are absent from the inventory. If those resource types exist in the environment, their logging and monitoring posture is unknown. (`evidence_gap:coverage:cloud-control-meta-collector-was-disabled-long-tail-resource-types-are-limited-to-typed-collector-coverage`)

**Operational action:** Re-enable Resource Explorer and Cloud Control meta-collectors in the next discovery run to close the coverage blind spot before drawing conclusions about logging completeness.

### Monitoring Gaps

Three monitoring gaps are relevant to platform operations:

**CloudWatch utilisation metrics not collected**
CloudoX does not collect CloudWatch utilisation metrics in this version. Idle, underutilised, or right-sizing signals based on actual runtime usage are not available. For operations engineers, this means there is no automated baseline for anomaly detection, capacity planning, or incident triage derived from this discovery run. (`evidence_gap:cost:cloudox-does-not-collect-cloudwatch-utilization-metrics-in-this-version-idle-underutilized-or-right-sizing-recommendations-based-on-actual-usage-are-not-available`)

**Specific service attributes not captured**
The following service-level attributes are outside current collector scope and therefore have no monitoring or cost-driver visibility in this run:
- RDS read replicas
- RDS provisioned IOPS
- DynamoDB capacity mode
- Direct Connect
- S3 storage classes

For operations, this means incidents or anomalies in these dimensions (e.g. a read replica falling behind, a DynamoDB table switching capacity modes unexpectedly) would not surface through CloudoX-derived signals. (`evidence_gap:cost:rds-read-replicas-rds-provisioned-iops-dynamodb-capacity-mode-direct-connect-and-s3-storage-classes-are-not-captured-by-the-current-collectors-so-cost-drivers-for-them-are-not-detected`)

**Tag-based observability is unreliable**
Only 1% of resources carry a configured cost-allocation tag. Beyond cost, this severely limits the ability to filter CloudWatch dashboards, alarms, or log groups by workload, environment, or team. Alert routing and ownership triage during incidents will rely on resource identifiers rather than tags. (`evidence_gap:cost:only-1-of-resources-carry-a-configured-cost-allocation-tag-so-tag-based-cost-allocation-is-not-yet-reliable`)

**Unassociated spend as a monitoring signal**
Approximately 22% of spend is in services that CloudoX cannot map to discovered architecture. This is reported as unassociated rather than force-fit. From an operations standpoint, this represents a portion of the environment whose resource lifecycle, scaling behaviour, and failure modes are not visible in this view. (`evidence_gap:cost:about-22-of-spend-is-in-services-cloudox-does-not-map-to-discovered-architecture-it-is-reported-as-unassociated-rather-than-force-fit`)

> **Coverage note (Likely confidence):** The 833 resources captured and the 3 internet-facing security groups (`sg-0459201826f8de5b3`, `sg-06f2b4190bf01d261`, `sg-0d6a48061beb72eae`) reflect typed-collector coverage only. The disabled meta-collectors mean additional resources may exist that are not represented here. Security group and IAM role counts (75 roles observed) should be validated against a full Resource Explorer sweep before being treated as complete.
