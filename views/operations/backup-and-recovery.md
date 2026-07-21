# Operations View — Backup & Recovery Signals

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Likely_

## Backup & Recovery Signals

The most operationally significant finding here is a single-AZ database instance with no standby — a zone failure would cause unplanned downtime and potential data loss with no automatic failover path.

### Backup Signals

No backup configuration signals (snapshot schedules, retention policies, or backup vault associations) are present in this section's package for any resource. This is a coverage gap, not a confirmed absence — the Resource Explorer and Cloud Control meta-collectors were both unavailable during discovery, which limits visibility into long-tail resource types and may mean backup configurations exist but were not captured. Treat this as an observability gap requiring manual validation.

### Recovery Readiness

One recovery risk is confirmed:

| Resource | Type | Finding | Impact |
|---|---|---|---|
| `cloudox-demo-atlas-prod-pg` | DBInstance | No Multi-AZ standby configured | Reduced availability; zone failure causes downtime or data loss |

**`cloudox-demo-atlas-prod-pg`** (`modernization_opportunity:architecture:cloudox-demo-atlas-prod-pg`) is a single-AZ datastore with no standby replica. If the availability zone hosting this instance experiences a failure, there is no automatic failover target — recovery would depend on a manual restore from the most recent snapshot, introducing both RTO and RPO risk. The recommended action is to evaluate enabling a Multi-AZ standby for this instance.

> **Coverage note (Likely confidence):** Two meta-collectors were unavailable during this discovery run — Resource Explorer and Cloud Control. This means the breadth of AWS resources visible to CloudoX could not be fully cross-checked, and long-tail resource types are limited to typed collector coverage. Additional datastores or backup-relevant resources may exist that are not reflected here. Validate directly in the AWS console or via CLI before treating this as a complete inventory.
