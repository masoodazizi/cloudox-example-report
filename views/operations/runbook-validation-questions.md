# Operations View — Runbook / Validation Questions

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Verified_

## Runbook / Validation Questions

Two IAM roles in account `161388682021` carry administrative privileges and require owner confirmation before they can be treated as intentionally scoped. Neither has a recorded owner or documented justification in the current discovery data. Resolve these before treating the account's IAM posture as fully understood.

> **Coverage note:** Resource Explorer and Cloud Control meta-collectors were unavailable during discovery. Long-tail resource types and AWS-visible breadth could not be cross-checked — the checks below reflect typed-collector coverage only. Additional roles or resources may exist outside this view's scope.

### Operational Checks

Both items below are **Assumed** confidence — CloudoX has observed the privilege level but cannot confirm intent without owner input. Treat them as open action items until validated.

| Role Friendly Name | Role ID (ref) | Account | Check Required |
|---|---|---|---|
| `cloudox-demo-sandbox-ci-admin` | `AROAAAAAAO5VMOEOZ70IX` | `161388682021` | Confirm admin privilege is intentional and role is actively owned |
| `cloudox-demo-sandbox-unused-admin` | `AROAAAAADPCL3BVEXUDTH` | `161388682021` | Confirm admin privilege is intentional; name suggests possible disuse |

The name `cloudox-demo-sandbox-unused-admin` is particularly worth prioritising — the "unused" prefix may indicate a role that was created for a temporary purpose and never decommissioned, though this cannot be confirmed from discovery data alone.

### Questions to Validate

Raise the following with the account owner or IAM administrator:

1. **`cloudox-demo-sandbox-ci-admin` (ref: `AROAAAAAAO5VMOEOZ70IX`)** — Does this CI role require its current administrative privilege level, and who is the designated owner responsible for it? If the CI pipeline only needs scoped permissions, the privilege level should be reduced.

2. **`cloudox-demo-sandbox-unused-admin` (ref: `AROAAAAADPCL3BVEXUDTH`)** — Does this role require its current administrative privilege level, and who owns it? If it is genuinely unused, it represents an unattended high-privilege identity and should be reviewed for removal or restriction.
