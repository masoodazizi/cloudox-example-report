# FinOps View — Cost Reference Appendix

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Unknown_

## Cost Reference Appendix

This appendix provides the cost-relevant entity inventory and provider-native evidence underpinning the FinOps view. Use it to trace any cost figure or optimization candidate back to a specific account or workload. No cost totals or spend figures are available in this section's package; those are covered in the analytical sections of this view.

> **Confidence: Unknown** — Entity identification is grounded in provider discovery, but cost attribution to workloads carries varying confidence levels (noted per entity below). Validate workload groupings marked *Likely* or *Assumed* with environment owners before using them as cost allocation boundaries.

### Cost Entity Reference

The following accounts and workloads are the cost-bearing entities identified in this environment. All are in the `cloudox-demo` workspace.

#### Accounts

| Account | Account ID | Confidence |
|---|---|---|
| Management Account | `110319895932` | Verified |
| Workload Prod Account | `122122642149` | Verified |
| Workload Dev Account | `105769365151` | Verified |
| Sandbox Ma Account | `161388682021` | Verified |
| Log Archive Account | `122980216815` | Likely |
| Platform Account | `150982215529` | Likely |
| Audit Account | `110019496666` | Likely |

Four accounts are Verified; three (Log Archive, Platform, Audit) are *Likely* — their account purpose classification should be confirmed before applying cost allocation rules.

#### Workloads

| Workload | Account | Region | Confidence |
|---|---|---|---|
| Cloudox (`cloudox`) | Workload Prod Account (`122122642149`) | eu-central-1 | Verified |
| Cloudox Demo Atlas Prod API (`cloudox-demo-atlas-prod-api`) | Workload Prod Account (`122122642149`) | eu-central-1 | Likely |
| Cloudox Demo Atlas Dev (`cloudox-demo-atlas-dev`) | Workload Dev Account (`105769365151`) | eu-central-1 | Likely |
| Cloudox Demo Atlas Dev API (`cloudox-demo-atlas-dev-api`) | Workload Dev Account (`105769365151`) | eu-central-1 | Likely |
| Cloudox Demo Sandbox Scratch (`cloudox-demo-sandbox-scratch`) | Sandbox Ma Account (`161388682021`) | eu-central-1 | Assumed |

Only the `cloudox` workload is Verified. The four remaining workloads are *Likely* or *Assumed* groupings — treat their cost attribution as indicative until validated. The Sandbox Scratch workload (`cloudox-demo-sandbox-scratch`) carries the lowest confidence (*Assumed*) and warrants explicit confirmation.

### Evidence

The provider-native resources anchoring this reference are two ECS clusters and their associated services, all in `eu-central-1`:

- **ECS Cluster** `cloudox-demo-atlas-dev` — in Workload Dev Account (`105769365151`)
  Evidence ref: `105769365151|eu-central-1|AWS::ECS::Cluster|arn:aws:ecs:eu-central-1:105769365151:cluster/cloudox-demo-atlas-dev`

- **ECS Service** `cloudox-demo-atlas-dev-api` — in Workload Dev Account (`105769365151`), within the dev cluster
  Evidence ref: `105769365151|eu-central-1|AWS::ECS::Service|arn:aws:ecs:eu-central-1:105769365151:service/cloudox-demo-atlas-dev/cloudox-demo-atlas-dev-api`

- **ECS Service** `cloudox-demo-atlas-prod-api` — in Workload Prod Account (`122122642149`)
  Evidence ref: `122122642149|eu-central-1|AWS::ECS::Service|arn:aws:ecs:eu-central-1:122122642149:service/cloudox-demo-atlas-prod/cloudox-demo-atlas-prod-api`

Relationship evidence (`3869aa71501448f98b6ced83e40d4235`, `96f9f34fd1594394a800f7160c83bc59`, `9c2924f01ffd402eaafca2bdf2d04d67`, `7aa166e18de34efd8699a663e13265f1`) links resources to their parent workloads and is used to support workload-level cost grouping. No billing or spend data is present in this evidence set.

### Changes Since Previous Snapshot

Between the snapshot at 11:50 UTC and the current snapshot at 12:54 UTC on 2026-07-20, several observed changes affect the cost-relevant entities in this reference:

- **ECS Service `cloudox-demo-atlas-dev-api`** (Workload Dev Account): desired and running task count scaled from 1 → 2. This doubles the compute footprint of the dev API service and will increase ECS/Fargate costs for this workload.
- **ECS Service `cloudox-demo-atlas-prod-api`** (Workload Prod Account): desired and running task count also scaled from 1 → 2. The same cost-doubling effect applies to the production API service.
- **ECS Cluster `cloudox-demo-atlas-dev`**: running tasks count increased from 1 → 2, consistent with the service-level change above.
- Workload membership relationships were updated: one resource (`96f9f34fd1594394a800f7160c83bc59`) was removed from the `cloudox-demo-atlas-dev` workload grouping, while two resources (`3869aa71501448f98b6ced83e40d4235`, `9c2924f01ffd402eaafca2bdf2d04d67`) were added to it. A further resource (`7aa166e18de34efd8699a663e13265f1`) was added to the `cloudox` workload. These relationship changes may shift cost attribution between workloads — validate current groupings before reconciling spend.

No additional changes beyond those listed above were recorded in this snapshot interval.
