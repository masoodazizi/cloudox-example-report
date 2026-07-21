# Security View — Validation Questions

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Verified_

## Validation Questions

Two open questions require direct confirmation from the environment owner before the privilege posture of account `161388682021` (cloudox-demo-sandbox) can be considered understood. Both relate to IAM roles carrying administrative-level privileges whose intent and ownership have not been established from available evidence alone.

**Confidence: Verified** — the roles exist and their privilege levels are confirmed; the *intent and ownership* behind them is what remains unresolved.

### Questions to Resolve

| # | Role | Account | Question to Confirm | Confidence |
|---|------|---------|--------------------|-----------|
| 1 | `cloudox-demo-sandbox-ci-admin` (`AROAAAAAAO5VMOEOZ70IX`) | 161388682021 | Does this role require its current administrative privilege level, and who owns it? | Assumed |
| 2 | `cloudox-demo-sandbox-unused-admin` (`AROAAAAADPCL3BVEXUDTH`) | 161388682021 | Does this role require its current administrative privilege level, and who owns it? | Assumed |

**`cloudox-demo-sandbox-ci-admin`** — The name suggests a CI/CD pipeline identity, which may legitimately require elevated permissions during deployment. However, whether *administrative* scope (as opposed to a scoped deployment role) is genuinely required has not been confirmed. Ownership is unknown from available evidence.

**`cloudox-demo-sandbox-unused-admin`** — The name explicitly signals this role may be unused, which raises a higher concern: an unowned, potentially dormant administrative role is a standing privilege risk regardless of whether it has been recently exercised. This warrants prompt clarification and, if confirmed unused, removal or at minimum disablement.

Both questions are rated **Assumed** confidence — the administrative privilege level is observed, but the business justification and accountable owner are not evidenced in the current discovery data. Neither question has been flagged as requiring an immediate decision, but both should be routed to the environment owner for written confirmation to close the governance gap.
