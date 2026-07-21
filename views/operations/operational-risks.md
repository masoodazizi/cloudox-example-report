# Operations View — Operational Risks

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Verified_

## Operational Risks

Four medium-severity risks are active across the environment, all centred on detection coverage gaps and over-privileged IAM roles. None require an immediate decision, but two of them — the GuardDuty and IAM Access Analyzer gaps — affect five accounts simultaneously and represent the highest operational exposure: incidents or policy violations in those accounts will be harder to detect and attribute.

### Highest Operational Risks

#### Uneven GuardDuty Threat Detection Coverage

GuardDuty is enabled in only 1 of 6 scanned accounts. The following five accounts have **no active threat detection** (`risk:security:aws-guardduty-detector`):

| Account | Account ID |
|---|---|
| Log Archive Account | 122980216815 |
| Workload Dev Account | 105769365151 |
| Workload Prod Account | 122122642149 |
| Management Account | 110319895932 |
| Sandbox Ma Account | 161388682021 |

The absence of GuardDuty in the Workload Prod Account (`122122642149`) and Management Account (`110319895932`) is particularly operationally significant — anomalous API calls, credential exfiltration, or network reconnaissance in those accounts will generate no automated findings. The recommended remediation is to enable GuardDuty org-wide via a delegated administrator, which avoids per-account configuration drift.

> **Coverage note:** The Log Archive Account (`122980216815`) is listed with `Likely` confidence — validate that GuardDuty is genuinely absent there before acting.

#### Uneven IAM Access Analyzer Coverage

IAM Access Analyzer is enabled in only 1 of 6 scanned accounts. The same five accounts listed above are uncovered (`risk:security:aws-accessanalyzer-analyzer`). Without Access Analyzer, externally-accessible resources (S3 buckets, IAM roles with cross-account trust, KMS keys, etc.) in those accounts will not be flagged automatically. The recommended remediation mirrors GuardDuty: enable org-wide via a delegated administrator.

> **Coverage note:** The same `Likely` confidence caveat applies to the Log Archive Account (`122980216815`).

#### Broadly-Privileged IAM Roles in Sandbox Ma Account

Two IAM roles in the Sandbox Ma Account (`161388682021`) carry names that suggest broad administrative access:

| Role Friendly Name | Role ID | Risk ID |
|---|---|---|
| cloudox-demo-sandbox-ci-admin | AROAAAAAAO5VMOEOZ70IX | `risk:security:cloudox-demo-sandbox-ci-admin` |
| cloudox-demo-sandbox-unused-admin | AROAAAAADPCL3BVEXUDTH | `risk:security:cloudox-demo-sandbox-unused-admin` |

Privilege breadth has **not been collected** — these risks are inferred from role naming and must be validated against attached policies before drawing conclusions. The `cloudox-demo-sandbox-unused-admin` name suggests the role may be dormant; if so, it is a candidate for removal rather than scoping. Both carry an `Assumed` confidence rating.

Recommended action for both: review attached policies, confirm actual permission scope, and apply least privilege. If `cloudox-demo-sandbox-unused-admin` is genuinely unused, delete it to reduce the identity blast radius.

---

**Coverage gaps affecting this section:** Resource Explorer and Cloud Control meta-collectors were unavailable during discovery. Long-tail resource types and full AWS-visible breadth could not be cross-checked — additional risks may exist that typed collectors did not surface.
