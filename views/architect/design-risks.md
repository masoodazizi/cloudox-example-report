# Architect View — Design Risks

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Likely_

## Design Risks

The most pressing architectural concern across this environment is the absence of consistent detective controls — GuardDuty and IAM Access Analyzer — in the majority of scanned accounts. Both gaps share the same blast radius: five of six accounts are uncovered, leaving workload, management, and sandbox tiers with materially weaker security posture than the single account where coverage exists.

### Architectural Risks

Two medium-severity, cross-account risks dominate this section.

**Uneven GuardDuty Coverage** (`risk:security:aws-guardduty-detector`)

GuardDuty threat detection is active in only 1 of 6 scanned accounts. The five accounts without coverage are:

| Account | Friendly Name | Confidence |
|---|---|---|
| 122980216815 | Log Archive Account | Likely |
| 105769365151 | Workload Dev Account | Verified |
| 122122642149 | Workload Prod Account | Verified |
| 110319895932 | Management Account | Verified |
| 161388682021 | Sandbox Ma Account | Verified |

The absence of GuardDuty in the Workload Prod Account (`122122642149`) and Management Account (`110319895932`) is architecturally significant: these are the accounts where anomalous API activity, credential misuse, or network reconnaissance would have the highest impact and where detection is most critical. The recommended path is org-wide enablement via a delegated administrator, which removes the per-account gap and centralises findings.

**Uneven IAM Access Analyzer Coverage** (`risk:security:aws-accessanalyzer-analyzer`)

IAM Access Analyzer is missing from the same five accounts. Without it, externally accessible resources (S3 buckets, IAM roles with cross-account trust, KMS keys, etc.) in those accounts are not continuously analysed for unintended public or cross-account exposure. Again, the Workload Prod Account and Management Account are the highest-priority gaps. Org-wide delegation is the recommended remediation, consistent with the GuardDuty recommendation.

Taken together, these two gaps represent a detective-control blind spot across the majority of the account estate. From an architecture standpoint, the pattern suggests that security tooling was enabled in one account (likely a security or audit account) but the org-wide delegation model was not completed.

### Technical Debt Signals

Two IAM roles in the Sandbox Ma Account (`161388682021`) carry names that suggest broad administrative privilege:

| Role Friendly Name | IAM Role ID | Risk ID |
|---|---|---|
| cloudox-demo-sandbox-ci-admin | AROAAAAAAO5VMOEOZ70IX | `risk:security:cloudox-demo-sandbox-ci-admin` |
| cloudox-demo-sandbox-unused-admin | AROAAAAADPCL3BVEXUDTH | `risk:security:cloudox-demo-sandbox-unused-admin` |

**Confidence: Assumed** — privilege breadth was not collected; these flags are inferred from role naming and must be validated against attached policies before acting.

The `cloudox-demo-sandbox-unused-admin` name is a particular signal: a role named "unused" that still exists in the account is a least-privilege debt item regardless of its actual policy scope. If it is genuinely unused, it should be removed; if it is active, the name is misleading and the policies need review.

The `cloudox-demo-sandbox-ci-admin` role suggests a CI/CD pipeline identity with potentially broad permissions. CI roles are a common lateral-movement vector if over-privileged; scoping this role to the minimum actions required by the pipeline is the recommended action.

Both roles are lower priority (3) than the detective-control gaps above, but they represent identity hygiene debt that compounds risk if the sandbox account is used for any integration or pre-production testing that touches shared infrastructure.

> **Note on classification confidence:** 781 resources across the environment have no Environment/Stage/Tier tag and rely on inference for workload classification. This limits the precision of impact scoping for the risks above — the true blast radius of the IAM roles, in particular, may be broader or narrower than naming alone suggests.
