# Security View — Identity & Access Risks

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Verified_

## Identity & Access Risks

The most pressing finding across this environment is the near-absence of automated detective controls: both GuardDuty and IAM Access Analyzer are active in only **1 of 6 scanned accounts**, leaving the Management Account (110319895932), Workload Dev (105769365151), Workload Prod (122122642149), Sandbox (161388682021), and Log Archive (122980216815) without consistent threat detection or external-access analysis. Two IAM roles in the Sandbox account carry names that strongly suggest broad administrative privilege and warrant immediate policy review.

### Privilege & Trust

Three IAM roles in the Sandbox account (161388682021) are relevant to privilege risk:

| Role | Principal ID | Concern |
|---|---|---|
| `cloudox-demo-sandbox-ci-admin` | `AROAAAAAAO5VMOEOZ70IX` | Name implies CI pipeline with admin-level access |
| `cloudox-demo-sandbox-unused-admin` | `AROAAAAADPCL3BVEXUDTH` | Name implies unused admin role — standing privilege with no apparent active use |

**Confidence: Assumed** — Privilege breadth for both roles is inferred from naming conventions; actual attached policies have not been collected. These findings must be validated by reviewing the policies directly before drawing conclusions about blast radius.

Recommended action for both: enumerate attached and inline policies, apply least-privilege scoping, and remove or disable `cloudox-demo-sandbox-unused-admin` if it has no active use case.

Several `OrganizationAccountAccessRole` instances are present across accounts (122980216815, 110019496666, 105769365151). These are standard AWS Organizations cross-account roles and are noted as key entities; their trust policies and usage patterns are not detailed in this section's package.

### Access Risks

**GuardDuty — Uneven threat detection coverage**
**Confidence: Likely** | Severity: Medium | `risk:security:aws-guardduty-detector`

GuardDuty is confirmed active in 1 of 6 scanned accounts. The five accounts without coverage — Management (110319895932), Workload Dev (105769365151), Workload Prod (122122642149), Sandbox (161388682021), and Log Archive (122980216815) — have reduced threat detection. The Workload Prod account is of particular concern given its likely sensitivity.

> **Recommended action:** Enable GuardDuty org-wide via a delegated administrator to ensure consistent coverage without per-account configuration.

---

**IAM Access Analyzer — Uneven external-access analysis coverage**
**Confidence: Likely** | Severity: Medium | `risk:security:aws-accessanalyzer-analyzer`

IAM Access Analyzer is active in 1 of 6 scanned accounts. The same five accounts listed above lack coverage, meaning resource policies granting external access (cross-account or public) will not be automatically flagged in those accounts.

> **Recommended action:** Enable IAM Access Analyzer org-wide via a delegated administrator, ideally with an organization-level analyzer to cover all accounts from a single pane.

---

**Broadly-privileged roles in Sandbox**
**Confidence: Assumed** | Severity: Medium

- `cloudox-demo-sandbox-ci-admin` (`AROAAAAAAO5VMOEOZ70IX`) — `risk:security:cloudox-demo-sandbox-ci-admin`
- `cloudox-demo-sandbox-unused-admin` (`AROAAAAADPCL3BVEXUDTH`) — `risk:security:cloudox-demo-sandbox-unused-admin`

Both roles are flagged on naming inference only. The "unused" label in the second role name raises an additional concern: a standing admin role with no active workload represents unnecessary persistent privilege. Validate whether either role has been assumed recently and whether admin-level permissions are genuinely required.

### Changes Since Previous Snapshot

Three IAM roles were added between the previous snapshot (2026-07-20T11:50) and the current one (2026-07-20T12:54):

- **`cloudox-demo-sandbox-ci-admin`** (`AROAAAAAAO5VMOEOZ70IX`) added to Sandbox account (161388682021) — this is the same role flagged as a privilege risk above. Its recent creation makes policy review more urgent.
- **`cloudox-demo-atlas-dev-ingest-sfn`** (`AROAAAAAAIJ1CH5G3USEC`) added to Workload Dev account (105769365151).
- **`cloudox-demo-atlas-prod-dr-replicator`** (`AROAAAAACFUR6W6I0LR0G`) added to Workload Prod account (122122642149).

Additional related changes exist beyond those listed here; see the Environment Evolution page for the full picture.
