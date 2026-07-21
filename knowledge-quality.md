# Knowledge Quality Intelligence

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

_Deterministic evaluation of whether each Knowledge View delivers useful cloud understanding to its audience. The scores below are the authoritative quality scores._

**Overall quality:** 92.1/100 (grade A)

- **Strongest view:** finops
- **Weakest view:** operations
- **Biggest issue:** The Executive View does not answer: "What decisions are needed?"

## View scorecards

| View | Score | Grade | Persona coverage | Findings |
| --- | ---: | :---: | ---: | ---: |
| Architect | 92.9 | A | 100% | 0 |
| Executive | 91.5 | A | 67% | 1 |
| FinOps | 96.2 | A | 100% | 1 |
| Generic | 93.4 | A | 100% | 0 |
| Operations | 87.7 | B | 100% | 1 |
| Security | 90.7 | A | 100% | 0 |

## Architect View — 92.9/100 (A)

_Audience: Solutions / Cloud Architects_

| Dimension | Score | Detail |
| --- | ---: | --- |
| persona coverage | 100 | 3/3 persona questions answered |
| hollow sections | 100 | 0/9 content-bearing sections are hollow |
| redundancy | 100 | 0 redundant unit(s) of 36 |
| signal to noise | 60 | signal-to-noise ratio 0.60 |
| actionability | 100 | 27/27 surfaced items are actionable |
| prioritization | 92 | 24/26 adjacent pairs correctly ordered |
| intelligence coverage | 100 | 3/3 expected intelligence kinds surfaced |

**Persona questions**

- ✓ What design issues exist?
- ✓ What dependencies matter?
- ✓ What modernization opportunities exist?

## Executive View — 91.5/100 (A)

_Audience: CTO / Engineering leadership_

| Dimension | Score | Detail |
| --- | ---: | --- |
| persona coverage | 67 | 2/3 persona questions answered |
| hollow sections | 100 | 0/8 content-bearing sections are hollow |
| redundancy | 100 | 0 redundant unit(s) of 25 |
| signal to noise | 95 | signal-to-noise ratio 0.95 |
| actionability | 100 | 17/17 surfaced items are actionable |
| prioritization | 94 | 15/16 adjacent pairs correctly ordered |
| intelligence coverage | 100 | 4/4 expected intelligence kinds surfaced |

**Persona questions**

- ✓ What requires leadership attention?
- ✓ What are the highest risks?
- ✗ What decisions are needed?

## FinOps View — 96.2/100 (A)

_Audience: FinOps / Finance_

| Dimension | Score | Detail |
| --- | ---: | --- |
| persona coverage | 100 | 3/3 persona questions answered |
| hollow sections | 88 | 1/8 content-bearing sections are hollow |
| redundancy | 100 | 0 redundant unit(s) of 21 |
| signal to noise | 88 | signal-to-noise ratio 0.88 |
| actionability | 100 | 16/16 surfaced items are actionable |
| prioritization | 100 | 15/15 adjacent pairs correctly ordered |
| intelligence coverage | 100 | 2/2 expected intelligence kinds surfaced |

**Persona questions**

- ✓ What is driving cost?
- ✓ What optimization is worth validating?
- ✓ Where is the waste?

## Generic View — 93.4/100 (A)

_Audience: Any technical reader_

| Dimension | Score | Detail |
| --- | ---: | --- |
| persona coverage | 100 | 3/3 persona questions answered |
| hollow sections | 100 | 0/9 content-bearing sections are hollow |
| redundancy | 100 | 0 redundant unit(s) of 29 |
| signal to noise | 62 | signal-to-noise ratio 0.62 |
| actionability | 100 | 19/19 surfaced items are actionable |
| prioritization | 94 | 17/18 adjacent pairs correctly ordered |
| intelligence coverage | 100 | 4/4 expected intelligence kinds surfaced |

**Persona questions**

- ✓ What is this environment and what runs in it?
- ✓ How is it structured, connected, and secured?
- ✓ What is uncertain or unknown?

## Operations View — 87.7/100 (B)

_Audience: Platform / Operations Engineers_

| Dimension | Score | Detail |
| --- | ---: | --- |
| persona coverage | 100 | 3/3 persona questions answered |
| hollow sections | 89 | 1/9 content-bearing sections are hollow |
| redundancy | 100 | 0 redundant unit(s) of 22 |
| signal to noise | 43 | signal-to-noise ratio 0.43 |
| actionability | 100 | 14/14 surfaced items are actionable |
| prioritization | 85 | 11/13 adjacent pairs correctly ordered |
| intelligence coverage | 100 | 2/2 expected intelligence kinds surfaced |

**Persona questions**

- ✓ What can break?
- ✓ What requires operational attention?
- ✓ What recovery risks exist?

## Security View — 90.7/100 (A)

_Audience: Security & Governance teams_

| Dimension | Score | Detail |
| --- | ---: | --- |
| persona coverage | 100 | 3/3 persona questions answered |
| hollow sections | 100 | 0/8 content-bearing sections are hollow |
| redundancy | 100 | 0 redundant unit(s) of 31 |
| signal to noise | 49 | signal-to-noise ratio 0.49 |
| actionability | 100 | 26/26 surfaced items are actionable |
| prioritization | 88 | 22/25 adjacent pairs correctly ordered |
| intelligence coverage | 100 | 4/4 expected intelligence kinds surfaced |

**Persona questions**

- ✓ What is exposed, and to whom?
- ✓ Where is identity / access over-privileged or unclear?
- ✓ What governance or evidence gaps exist?

## Quality findings

- **[warn] Executive View does not answer a persona question** (executive) — The Executive View does not answer: "What decisions are needed?"
  - _Recommendation:_ Surface intelligence (or interpretation) that addresses this audience question, or remove the question from the view's scope.
- **[info] Hollow section: Waste / Utilization Signals** (finops/waste-and-utilization) — FinOps → Waste / Utilization Signals declares operational_concern but the environment produced none to surface.
  - _Recommendation:_ Populate the section, broaden its declared kinds/domains, or drop it from the view so the report has no empty shells.
- **[info] Hollow section: Failure Points** (operations/failure-points) — Operations → Failure Points declares operational_concern but the environment produced none to surface.
  - _Recommendation:_ Populate the section, broaden its declared kinds/domains, or drop it from the view so the report has no empty shells.
