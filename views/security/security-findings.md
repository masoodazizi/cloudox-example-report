# Security View — Security Findings

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Verified_

## Security Findings

**Confidence: Verified** — Derived from complete graph evidence for this domain.

The findings surfaced in this section are cost-pressure signals on four workloads across two accounts (`122122642149` and `105769365151`) in `eu-central-1`. While these are categorised under the cost domain, they are included here as prioritisation signals that security and governance teams should be aware of when scoping resource-owner reviews. No direct security misconfigurations, exposure findings, or identity findings are contained in this section's package — those are covered in other sections of this view.

### Findings

Four workloads have been flagged as cost-review candidates. All carry **priority 4** and are grounded at **Assumed** or **Unknown** confidence — meaning the workload groupings and cost allocations are inferred, not directly observed. Treat these as prioritisation signals only; validate with resource owners before acting.

| Workload | Account | Ranking Signal | Cost Drivers | Confidence |
|---|---|---|---|---|
| Cloudox (`cloudox`) | `122122642149` | 0.80 / 1.0 | 3 architectural | Assumed |
| Cloudox Demo Atlas Dev (`cloudox-demo-atlas-dev`) | `105769365151` | 0.23 / 1.0 | 1 architectural | Assumed |
| Cloudox Demo Atlas Dev API (`cloudox-demo-atlas-dev-api`) | `105769365151` | 0.18 / 1.0 | 1 architectural | Unknown |
| Cloudox Demo Atlas Prod API (`cloudox-demo-atlas-prod-api`) | `122122642149` | 0.18 / 1.0 | 1 architectural | Unknown |

**Cloudox** (`recommendation:cost:cloudox`) carries the highest relative cost pressure (ranking signal 0.80/1.0) with 3 architectural cost drivers identified. An inferred allocated share of ~41 is associated with it — this is not an exact workload cost figure. **Confidence: Assumed.**

**Cloudox Demo Atlas Dev** (`recommendation:cost:cloudox-demo-atlas-dev`) shows a ranking signal of 0.23/1.0 with 1 architectural cost driver and an inferred allocated share of ~5. **Confidence: Assumed.**

**Cloudox Demo Atlas Dev API** (`recommendation:cost:cloudox-demo-atlas-dev-api`) shows a ranking signal of 0.18/1.0 with 1 architectural cost driver. No allocated share figure is available for this workload. **Confidence: Unknown** — the workload boundary and cost attribution have not been confirmed.

**Cloudox Demo Atlas Prod API** (`recommendation:cost:cloudox-demo-atlas-prod-api`) shows a ranking signal of 0.18/1.0 with 1 architectural cost driver. No allocated share figure is available. **Confidence: Unknown** — same caveat as above.

No optimization candidates (e.g. idle or rightsizing opportunities) were identified for any of the four workloads in this package.

### Recommended Mitigations

Given the Assumed/Unknown confidence on all four findings, the appropriate action is validation before any remediation:

- **Engage resource owners** for each of the four workloads to confirm workload boundaries and the architectural cost drivers identified. This is especially important for `cloudox-demo-atlas-dev-api` and `cloudox-demo-atlas-prod-api`, where workload attribution is Unknown.
- **Prioritise the Cloudox workload** (`cloudox`, account `122122642149`) for the first review cycle — it carries the highest ranking signal (0.80/1.0) and the most architectural cost drivers (3), making it the strongest candidate for a structured cost and architecture review.
- **Do not act on inferred allocated share figures** as exact cost data. Use them only to sequence review conversations with resource owners.
- Security and governance teams should note that the evidence references for this section include IAM roles and security groups (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin`, `arn:aws:iam::161388682021:role/cloudox-demo-sandbox-unused-admin`, `arn:aws:ec2:eu-central-1:122122642149:security-group/sg-0459201826f8de5b3`, and others) — these are available as supporting evidence for cross-referencing workload ownership during reviews, but their security-specific analysis is covered in the identity and exposure sections of this view.
