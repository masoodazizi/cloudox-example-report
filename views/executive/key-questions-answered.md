# Executive View — Key Questions Answered

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Unknown_

## Key Questions Answered

### What requires leadership attention right now?

The most pressing items are gaps in foundational security controls spanning the majority of accounts. GuardDuty threat detection and IAM Access Analyzer are each active in only 1 of 6 scanned accounts — leaving the Workload Prod Account, Workload Dev Account, Management Account, Sandbox Ma Account, and Log Archive Account without consistent threat detection or external-access analysis. These are not configuration edge cases; they represent a structural blind spot across the estate that warrants a directed remediation.

Confidence: Likely — coverage gaps are observed in 5 accounts; the one covered account is confirmed.

### What are the highest business and security risks?

Three categories of risk are present:

1. **Broad detective control gaps** — GuardDuty and IAM Access Analyzer missing from 5 accounts (`risk:security:aws-guardduty-detector`, `risk:security:aws-accessanalyzer-analyzer`). Without these, malicious activity and unintended external resource access may go undetected.

2. **Internet-exposed production surface** — The production load balancer (`cloudox-demo-atlas-prod-alb`) is internet-facing, and three security groups have world-open ingress rules: `cloudox-demo-atlas-prod-sg-edge` (port 443, Workload Prod Account), `cloudox-demo-atlas-prod-sg-alb` (port 80, Workload Prod Account), and `cloudox-demo-atlas-dev-sg-ecs` (port 80, Workload Dev Account). The HTTPS exposure on the edge group may be intentional for a public API; the HTTP exposures on ports 80 should be confirmed or closed.

3. **Over-privileged IAM roles in Sandbox** — Two roles in the Sandbox Ma Account — `cloudox-demo-sandbox-ci-admin` and `cloudox-demo-sandbox-unused-admin` — are named to suggest broad administrative access. Actual privilege breadth has not been collected, so this is inferred from naming and requires validation. The "unused-admin" name in particular warrants prompt review.

Confidence: Verified for internet exposure items; Assumed for IAM role privilege breadth (naming-based inference only).

### What decisions need leadership input?

No items in this section are flagged as requiring an immediate leadership decision gate. However, two policy-level choices would benefit from leadership alignment:

- **Org-wide security tooling mandate**: Enabling GuardDuty and IAM Access Analyzer via a delegated administrator at the organization level is the recommended path. This is an architectural and governance decision, not just an operational task.
- **Internet exposure policy for production**: The team should confirm whether the HTTP ingress rules on production and dev security groups are intentional. If not, they should be restricted. Leadership should set the expectation for a documented decision on each open exposure.

Confidence: Likely — recommendations are grounded in observed gaps; the governance model is not confirmed in this package.

### Where is spend concentrated and where can we be more efficient?

Total observed spend across the scanned accounts is approximately **$80.28 USD** in the captured period. Eight architectural cost drivers and one optimization candidate have been identified. Detailed cost breakdown and optimization specifics are covered in the Cost sections of this view — this section does not carry the per-service or per-account spend breakdown needed to answer this question fully.

Confidence: Unknown for spend distribution detail — cost driver and optimization analysis is available in the dedicated cost sections.

### What should we act on next?

In priority order:

1. **Enable GuardDuty org-wide** — highest leverage, single action covers 5 accounts. (`risk:security:aws-guardduty-detector`)
2. **Enable IAM Access Analyzer org-wide** — same scope, same mechanism. (`risk:security:aws-accessanalyzer-analyzer`)
3. **Review and confirm internet-facing HTTP ingress rules** — ports 80 open to the world on `cloudox-demo-atlas-prod-sg-alb` and `cloudox-demo-atlas-dev-sg-ecs` should be validated or restricted. (`security_exposure:security:sg-06f2b4190bf01d261`, `security_exposure:security:sg-0d6a48061beb72eae`)
4. **Audit sandbox admin roles** — validate actual permissions on `cloudox-demo-sandbox-ci-admin` and `cloudox-demo-sandbox-unused-admin`; remove or scope down if over-privileged. (`risk:security:cloudox-demo-sandbox-ci-admin`, `risk:security:cloudox-demo-sandbox-unused-admin`)

### What important gaps remain unverified?

- **IAM role privilege breadth**: The two sandbox admin roles are flagged based on naming convention only. Actual attached policies have not been collected. This must be validated directly before drawing conclusions about blast radius.
- **Log Archive Account coverage**: Inclusion of the Log Archive Account in the GuardDuty and Access Analyzer gap count is rated Likely, not Verified — its status should be confirmed.
- **Audit Account**: The Audit Account is identified as a known account but was excluded from this scan scope. Its security control posture is not assessed here.
- **Cost distribution**: Per-account and per-service spend detail is not available in this section; refer to the cost sections for efficiency decisions.
