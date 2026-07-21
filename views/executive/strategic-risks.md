# Executive View — Strategic Risks

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Verified_

## Strategic Risks

### Business & Security Risks

The most significant finding across the organisation is a **systematic gap in core AWS security controls**: GuardDuty threat detection and IAM Access Analyzer are each enabled in only 1 of 6 scanned accounts, leaving 5 accounts — including the Management Account, both Workload accounts (Prod and Dev), the Log Archive Account, and the Sandbox — without consistent threat detection or external-access analysis. This is not an isolated misconfiguration; it is a structural coverage gap that reduces the organisation's ability to detect compromise or unintended resource exposure across the majority of its AWS footprint.

| Risk | Accounts Affected | Severity | Recommended Action |
|---|---|---|---|
| GuardDuty not enabled | 5 of 6 (incl. Prod, Management) | Medium | Enable org-wide via delegated administrator |
| IAM Access Analyzer not enabled | 5 of 6 (incl. Prod, Management) | Medium | Enable org-wide via delegated administrator |
| Broadly-privileged CI role (`cloudox-demo-sandbox-ci-admin`) | Sandbox Ma Account | Medium | Review and apply least privilege |
| Broadly-privileged unused admin role (`cloudox-demo-sandbox-unused-admin`) | Sandbox Ma Account | Medium | Review and remove or scope down |

The two GuardDuty and Access Analyzer gaps are **Likely** confidence — derived from account-level discovery — and represent the highest-priority items for engineering leadership to address. Enabling both controls organisation-wide through a delegated administrator in the Management Account is the recommended path, as it closes the gap across all accounts in a single action.

Two IAM roles in the Sandbox Ma Account — `cloudox-demo-sandbox-ci-admin` and `cloudox-demo-sandbox-unused-admin` — carry names strongly suggesting broad administrative privilege. These are flagged at **Assumed** confidence: privilege breadth has not been directly collected and must be validated by reviewing attached policies. The presence of an apparently unused admin role (`cloudox-demo-sandbox-unused-admin`) is a particular concern; if it is genuinely unused, it represents unnecessary standing privilege that should be removed.

**The decision needed from leadership:** Prioritise org-wide enablement of GuardDuty and IAM Access Analyzer, and direct the team responsible for the Sandbox account to validate and remediate the two broadly-named admin roles. Neither action requires architectural change — both are operational hygiene decisions that can be executed quickly.

> **Note on classification confidence:** 781 resources across the environment have no Environment/Stage/Tier tag and rely on inference for workload classification. This does not affect the risk findings above, but limits the precision of environment-level reporting more broadly.
