# Architect View — Assumptions & Unknowns

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Likely_

## Assumptions & Unknowns

Two categories of limitation shape how much confidence to place in this view: **coverage gaps** (what CloudoX could not collect) and **open validation questions** (things discovered but not yet confirmed with environment owners). Architects should treat findings in affected areas as directionally correct but not exhaustive.

### Assumptions Made

No explicit assumptions were injected into this section's analysis. However, one material inference underpins workload and environment classification throughout the view:

- **781 resources carry no Environment / Stage / Tier tag.** Their classification into environments (dev, prod, sandbox, etc.) relies on inference — typically from naming conventions, account boundaries, or graph relationships — rather than authoritative tagging. Only 1% of resources carry a configured cost-allocation tag, so tag-based cost allocation is similarly unreliable at this time. [`evidence_gap:cost:only-1-of-resources-carry-a-configured-cost-allocation-tag-so-tag-based-cost-allocation-is-not-yet-reliable`]

### Open Questions

**Discovery coverage gaps** — the following gaps mean the architecture picture may be incomplete in specific areas:

| Gap | What it means for architects |
|---|---|
| Cloud Control meta-collector was disabled | Long-tail resource types (those not covered by typed collectors) may be absent from the graph. The full resource inventory breadth is unknown. [`evidence_gap:coverage:cloud-control-meta-collector-was-disabled-long-tail-resource-types-are-limited-to-typed-collector-coverage`] |
| Resource Explorer meta-collector was disabled or unavailable | AWS-visible resource breadth could not be cross-checked, so undiscovered resources cannot be ruled out. [`evidence_gap:coverage:resource-explorer-meta-collector-was-disabled-or-unavailable-aws-visible-breadth-could-not-be-cross-checked`] |
| ~22% of spend is in services not mapped to discovered architecture | That portion of cost is reported as unassociated rather than attributed to a workload. Architectural cost attribution is incomplete for those services. [`evidence_gap:cost:about-22-of-spend-is-in-services-cloudox-does-not-map-to-discovered-architecture-it-is-reported-as-unassociated-rather-than-force-fit`] |
| CloudWatch utilization metrics not collected | Idle, underutilized, or right-sizing signals based on actual usage are not available in this version. [`evidence_gap:cost:cloudox-does-not-collect-cloudwatch-utilization-metrics-in-this-version-idle-underutilized-or-right-sizing-recommendations-based-on-actual-usage-are-not-available`] |
| RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes not captured | Cost drivers and configuration details for these specific resource attributes are not detected by current collectors. [`evidence_gap:cost:rds-read-replicas-rds-provisioned-iops-dynamodb-capacity-mode-direct-connect-and-s3-storage-classes-are-not-captured-by-the-current-collectors-so-cost-drivers-for-them-are-not-detected`] |

**IAM validation questions** — the following roles in the sandbox account (111588830789) carry administrative privileges and require owner confirmation before architectural trust boundaries can be drawn with confidence:

- **cloudox-demo-sandbox-ci-admin** (`AROAAAAAAO5VMOEOZ70IX`): Is this CI/CD role's current privilege level intentional, and who owns it? [`validation_question:security:cloudox-demo-sandbox-ci-admin`]
- **cloudox-demo-sandbox-unused-admin** (`AROAAAAADPCL3BVEXUDTH`): Does this role require its current privilege level, and is it still in use? [`validation_question:security:cloudox-demo-sandbox-unused-admin`]

Both are flagged at **Assumed** confidence — the privilege level is observed, but intent and ownership are unconfirmed. Resolving these with the environment owner is recommended before finalising security architecture conclusions for the sandbox environment.
