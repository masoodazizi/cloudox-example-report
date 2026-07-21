# Executive View — Evidence Appendix

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Unknown_

## Evidence Appendix

### Key Entities

The following AWS accounts and workloads underpin this view:

| Friendly Name | Type | Confidence |
|---|---|---|
| Management Account | Account | Verified |
| Workload Prod Account | Account | Verified |
| Workload Dev Account | Account | Verified |
| Sandbox Ma Account | Account | Verified |
| Log Archive Account | Account | Likely |
| Cloudox Demo Atlas Prod API | Workload (eu-central-1) | Likely |

The Log Archive Account and the Cloudox Demo Atlas Prod API workload carry a **Likely** confidence — treat any findings tied to these entities as directionally sound but worth confirming with the environment owner.

### Evidence

No intelligence items are surfaced in this appendix. The provider-native evidence underpinning this view traces to the **Cloudox Demo Atlas Prod API** ECS service running in the Workload Prod Account (eu-central-1).

### Changes Since Previous Snapshot

Between the 11:50 and 12:54 UTC snapshots on 2026-07-20, one observed change was recorded:

- **Cloudox Demo Atlas Prod API** (ECS Service, Workload Prod Account, eu-central-1): desired and running task count scaled from **1 → 2**.
