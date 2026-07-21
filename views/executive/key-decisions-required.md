# Executive View — Key Decisions Required

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Verified_

## Key Decisions Required

### Decisions for Leadership

The environment carries a confirmed spend of **$80.28** across completed billing periods, with **8 architectural cost drivers** and **1 optimization candidate** already identified. Three areas warrant leadership attention before the next planning cycle.

**1. Resolve the tagging gap before cost attribution becomes unmanageable.**
781 resources carry no Environment, Stage, or Tier tag and are classified by inference rather than explicit ownership. Without authoritative tags, cost accountability, environment-level budgeting, and incident blast-radius analysis all depend on heuristics. A tagging policy with enforcement (e.g., AWS Config rules or SCP guardrails) is a decision that needs an owner and a deadline.

**2. Address open security group exposure.**
Three security groups — across two accounts in eu-central-1 — are present in the evidence set and should be reviewed for overly permissive ingress rules. Decisions on scope reduction or compensating controls belong with engineering leadership, not individual teams, to ensure consistent risk tolerance is applied.

**3. Decide on the ~22% unassociated spend.**
Roughly one-fifth of spend is in services CloudoX cannot map to discovered architecture. This is reported as unassociated rather than attributed. Leadership should decide whether to invest in tagging/CUR enrichment to close this visibility gap, or accept the blind spot. Until resolved, cost-reduction targets and architectural decisions rest on an incomplete picture.

**Supporting context — what is not yet answerable:**
Right-sizing and idle-resource decisions cannot be made today because CloudWatch utilization metrics are not collected in this version. The 1 optimization candidate identified is based on architectural pattern analysis, not measured usage. Committing to right-sizing actions before utilization data is available carries risk of over-reduction.

> **Confidence: Verified** — spend figures and security group evidence are provider-derived. The tagging gap count and unassociated spend percentage are drawn directly from discovery data. Utilization-based recommendations are explicitly out of scope for this version.
