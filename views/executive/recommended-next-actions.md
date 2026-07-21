# Executive View — Recommended Next Actions

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Verified_

## Recommended Next Actions

### Near-Term Actions

Three workloads and one production datastore surface as the highest-priority items requiring owner engagement. None demand an immediate architectural decision, but two warrant prompt validation before any cost or resilience action is taken.

**Production database resilience — act with confidence**

The production database `cloudox-demo-atlas-prod-pg` runs in a single Availability Zone with no Multi-AZ standby. A zone failure would cause downtime and potential data loss. This is the only item in this section with Verified confidence. The recommended step is to evaluate enabling a Multi-AZ standby; the decision sits with engineering leadership and the database owner. (Ref: `modernization_opportunity:architecture:cloudox-demo-atlas-prod-pg`)

**Cost pressure — validate before acting**

Three workloads carry the highest relative cost-pressure signals in the environment. These are prioritization signals derived from architectural cost drivers, not exact spend figures — validate with resource owners before drawing conclusions or taking action.

| Workload | Region | Cost-pressure signal | Architectural cost drivers | Confidence |
|---|---|---|---|---|
| Cloudox | eu-central-1 | 0.80 / 1.0 (highest) | 3 | Assumed |
| Cloudox Demo Atlas Dev | eu-central-1 | 0.23 / 1.0 | 1 | Assumed |
| Cloudox Demo Atlas Dev API | eu-central-1 | 0.18 / 1.0 | 1 | Unknown |

The recommended action for each is to review cost drivers with the respective resource owners before any optimization move. (Refs: `recommendation:cost:cloudox`, `recommendation:cost:cloudox-demo-atlas-dev`, `recommendation:cost:cloudox-demo-atlas-dev-api`)

**Material gaps that limit this picture**

Four constraints bound what CloudoX can surface here and are worth flagging to engineering leadership:

- CloudWatch utilization metrics are not collected in this version, so idle-resource and right-sizing recommendations are not available.
- Only 1% of resources carry a cost-allocation tag, making tag-based cost attribution unreliable across the board.
- Approximately 22% of spend maps to services outside the discovered architecture and is reported as unassociated rather than attributed.
- Several service categories (RDS read replicas, provisioned IOPS, DynamoDB capacity mode, Direct Connect, S3 storage classes) are not captured by current collectors.
