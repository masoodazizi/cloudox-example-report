# Security View — Security Overview

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Verified_

## Security Overview

Three security groups are currently open to the internet across the assessed accounts, and 75 IAM roles exist with no customer-managed policies in evidence — both are the leading risk signals for this environment. 833 resources were captured across the organisation with no coverage gaps identified within the scope of typed collectors, though two meta-collector gaps mean the true resource breadth cannot be fully confirmed (see Scope of Assessment below).

**Confidence: Verified** — derived from complete graph evidence for this domain.

---

### Security Posture

**Internet-exposed security groups** are the most immediate network-level concern. Three security groups carry rules that permit inbound access from the internet:

| Security Group | Account | Account Name |
|---|---|---|
| `sg-0459201826f8de5b3` | 122122642149 | Workload Prod Account |
| `sg-06f2b4190bf01d261` | 122122642149 | Workload Prod Account |
| `sg-0d6a48061beb72eae` | 105769365151 | Workload Dev Account |

Two of these (`sg-0459201826f8de5b3`, `sg-06f2b4190bf01d261`) are in the Production environment (122122642149). The presence of internet-exposed groups in production warrants validation that each open rule is intentional and scoped to the minimum required port range and source.

**IAM roles** — 75 roles are present across the organisation. Notable roles with elevated or unclear privilege include:

- `cloudox-demo-sandbox-ci-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin`) — CI role with admin-level naming in the Sandbox Ma Account (161388682021). Confirm blast radius if credentials are compromised.
- `cloudox-demo-sandbox-unused-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-unused-admin`) — The name explicitly signals this role may be unused. An unused admin role is an unnecessary standing privilege and should be reviewed for removal.
- `OrganizationAccountAccessRole` (`arn:aws:iam::161388682021:role/OrganizationAccountAccessRole`) — Standard AWS Organizations cross-account access role. Confirm that only authorised principals in the Management Account (110319895932) can assume it.
- `cloudox-demo-org-trail-logs` (`arn:aws:iam::110319895932:role/cloudox-demo-org-trail-logs`) — Scoped to CloudTrail log delivery in the Management Account; lower risk but should be confirmed as least-privilege.
- `AWSServiceRoleForCloudFormationStackSetsOrgAdmin` (`arn:aws:iam::110319895932:role/aws-service-role/stacksets.cloudformation.amazonaws.com/AWSServiceRoleForCloudFormationStackSetsOrgAdmin`) — AWS-managed service-linked role for StackSets org administration. Expected if StackSets is in use; validate it is still required.
- `cloudox-demo-sandbox-scratch-lambda` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-scratch-lambda`) — Lambda execution role in the sandbox. The "scratch" naming suggests an ad-hoc or temporary role; confirm it is still needed and appropriately scoped.

**No customer-managed IAM policies** were found. All role permissions are therefore delivered through AWS-managed policies or inline policies. This limits the ability to audit, version-control, and consistently apply permission boundaries across the organisation — a governance gap worth addressing.

**CloudTrail** — An organisation-level trail (`cloudox-demo-org-trail-o-aaaapzvebq`, `arn:aws:cloudtrail:eu-central-1:147790488693:trail/cloudox-demo-org-trail-o-aaaapzvebq`) is present in the Management Account (110319895932), providing a baseline audit log across the organisation. Detailed trail configuration and log integrity settings are covered in the dedicated audit/logging section of this view.

---

### Scope of Assessment

833 resources were captured across the following accounts:

| Account ID | Friendly Name | Confidence |
|---|---|---|
| 110319895932 | Management Account | Verified |
| 161388682021 | Sandbox Ma Account | Verified |
| 105769365151 | Workload Dev Account | Verified |
| 122122642149 | Workload Prod Account | Verified |
| 122980216815 | Log Archive Account | Likely |
| 110019496666 | Audit Account | Likely |
| 150982215529 | Platform Account | Likely |

Four accounts are Verified (full discovery evidence). Three accounts — Log Archive (122980216815), Audit (110019496666), and Platform (150982215529) — are rated **Likely**, meaning their classification is inferred rather than directly confirmed. Security findings attributed to those accounts should be treated with that caveat.

**Coverage gaps (material unknowns):**

- **Resource Explorer meta-collector was disabled or unavailable.** AWS-visible resource breadth could not be cross-checked against the typed collector inventory. Resources of types not covered by typed collectors may be absent from this assessment.
- **Cloud Control meta-collector was disabled.** Long-tail resource types (those without a dedicated typed collector) are not represented. This means the 833-resource count is a lower bound, not a complete inventory.

These gaps do not invalidate the findings above, but they mean the security posture assessment cannot be considered exhaustive. Enabling both meta-collectors in a future discovery run is recommended to close this blind spot.

---

### Changes Since Previous Snapshot

Between the previous snapshot (2026-07-20T11:50 UTC) and the current snapshot (2026-07-20T12:54 UTC), the following security-relevant changes were observed:

- **New internet exposure (action required):** Security group `sg-0d6a48061beb72eae` (Workload Dev Account, 105769365151) became reachable from the internet. This is a new exposure that was not present in the prior snapshot and should be validated immediately.
- **Exposure removed:** Security group `sg-04fae132cfc68e91d` is no longer reachable from the internet. This reduces the exposed surface relative to the previous snapshot.
- **New services entered discovered scope:** `securityhub`, `ssm`, and `stepfunctions` all appeared in the discovered scope for the first time. Their presence indicates either new enablement or that discovery coverage was extended to reach them. Security Hub entering scope is particularly relevant — its findings will now be available for analysis in subsequent view runs.
