# Generic View — Key Workloads

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Likely_

## Key Workloads

Five workloads are identified across this environment, spanning production, development, and sandbox contexts — all anchored in `eu-central-1`. The production API carries a notable resilience concern that warrants attention before any availability commitments are made.

### Significant Workloads

The following workloads are recognised in the environment:

| Friendly Name | Account | Region | Confidence |
|---|---|---|---|
| Cloudox | `122122642149` | eu-central-1 | Verified |
| Cloudox Demo Atlas Prod API | `122122642149` | eu-central-1 | Likely |
| Cloudox Demo Atlas Dev | `105769365151` | eu-central-1 | Likely |
| Cloudox Demo Atlas Dev API | `105769365151` | eu-central-1 | Likely |
| Cloudox Demo Sandbox Scratch | `161388682021` | eu-central-1 | Assumed |

**Cloudox** (`cloudox`) is the only Verified workload — its composition is well-evidenced in the graph. The Atlas Dev and Prod API workloads are Likely, meaning their boundaries are graph-derived but not fully confirmed. **Cloudox Demo Sandbox Scratch** (`cloudox-demo-sandbox-scratch`) carries an Assumed confidence; treat its classification as provisional until validated with the environment owner.

The environment spans at least three AWS accounts (`122122642149`, `105769365151`, `161388682021`). Internet-facing entry points are present: two API Gateway endpoints (`https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`, `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`) and internet gateways in both `eu-central-1` and `us-east-1` are in scope as evidence references, though their precise workload assignments are covered in other sections.

### Composition & Tiers

The Atlas workloads are built on ECS. The dev environment runs on the ECS cluster `cloudox-demo-atlas-dev` (`arn:aws:ecs:eu-central-1:105769365151:cluster/cloudox-demo-atlas-dev`), with the service `cloudox-demo-atlas-dev-api` (`arn:aws:ecs:eu-central-1:105769365151:service/cloudox-demo-atlas-dev/cloudox-demo-atlas-dev-api`) handling the API tier. The production counterpart is `cloudox-demo-atlas-prod-api` (`arn:aws:ecs:eu-central-1:122122642149:service/cloudox-demo-atlas-prod/cloudox-demo-atlas-prod-api`).

Data tiers are evidenced by DynamoDB tables:
- **Dev account (`105769365151`):** `cloudox-demo-atlas-dev-items` (`arn:aws:dynamodb:eu-central-1:105769365151:table/cloudox-demo-atlas-dev-items`)
- **Prod account (`122122642149`):** `cloudox-demo-atlas-prod-items` (`arn:aws:dynamodb:eu-central-1:122122642149:table/cloudox-demo-atlas-prod-items`)
- **Sandbox account (`161388682021`):** `cloudox-demo-sandbox-events` and `cloudox-demo-sandbox-scratch` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-events`, `arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-scratch`)

> **Note:** 781 resources have no Environment / Stage / Tier tag and rely on inference for classification. Workload boundaries and tier assignments for untagged resources should be treated as approximate.

### Key Dependencies

> **Resilience concern — Priority 3 (Verified)**

The **Cloudox Demo Atlas Prod API** workload (`cloudox-demo-atlas-prod-api`, account `122122642149`, eu-central-1) depends on the RDS DB instance **`cloudox-demo-atlas-prod-pg`**, which is not configured for Multi-AZ. A single availability zone failure would take the production data tier offline for this workload.

- **Impact:** Single-AZ datastore in a workload's critical path.
- **Recommended action:** Confirm whether Multi-AZ is enabled and backups are in place; review how the dependent workload handles a data-tier failure.
- **Confidence on the dependency:** Verified. The resilience posture of `cloudox-demo-atlas-prod-pg` itself (account, region, current backup configuration) is not fully evidenced in this section's package — confirm directly with the environment owner.

No other dependency concerns are recorded in this section's package.

### Changes Since Previous Snapshot

Between the snapshots at `2026-07-20T11:50` and `2026-07-20T12:54` (UTC), several observed changes affected the Atlas workloads:

- **`cloudox-demo-atlas-dev-api`** (ECS Service): desired and running task counts increased from 1 → 2. The parent cluster **`cloudox-demo-atlas-dev`** correspondingly shows running tasks rising from 1 → 2.
- **`cloudox-demo-atlas-prod-api`** (ECS Service): desired and running task counts also increased from 1 → 2.
- Component membership within the **`cloudox-demo-atlas-dev`** workload shifted: one `part_of` / `part_of_workload` relationship (`96f9f34fd1594394a800f7160c83bc59`) was removed, and two new ones (`3869aa71501448f98b6ced83e40d4235`, `9c2924f01ffd402eaafca2bdf2d04d67`) were added.
- A new `part_of_workload` relationship (`7aa166e18de34efd8699a663e13265f1`) was added to the **Cloudox** workload.

Additional related changes exist beyond what is summarised here — see the Environment Evolution page for the full picture.
