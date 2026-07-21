# Operations View — Critical Components

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Likely_

## Critical Components

The most operationally significant finding in this section is a single-AZ database instance sitting directly in the critical path of a production workload — a zone failure would take that workload's data tier offline with no automatic failover.

### Critical Workloads

The **Cloudox Demo Atlas Prod API** workload (`cloudox-demo-atlas-prod-api`, account `122122642149`, eu-central-1) has a verified dependency on the DBInstance **`cloudox-demo-atlas-prod-pg`**. That datastore is not configured for Multi-AZ. The operational consequence is straightforward: if the availability zone hosting `cloudox-demo-atlas-prod-pg` experiences a failure, the Atlas Prod API loses its data tier entirely until the instance is recovered or manually failed over.

**What can break:** Any request path through Cloudox Demo Atlas Prod API that touches the database will fail. There is no automatic promotion to a standby replica because none is confirmed to exist.

**What requires operational attention:**
- Confirm whether Multi-AZ is enabled on `cloudox-demo-atlas-prod-pg`. If it is not, this is a gap that needs remediation.
- Verify that automated backups are in place and that the recovery time objective (RTO) is acceptable for a manual restore scenario.
- Review how the Cloudox Demo Atlas Prod API handles data-tier unavailability — does it degrade gracefully, queue writes, or fail hard to callers?

**Recovery risk:** Without Multi-AZ, recovery from a zone failure depends on a point-in-time restore or snapshot, which carries both RTO and RPO risk. The recommended action is to confirm resilience posture (Multi-AZ / backup schedule) and validate dependent workload failure-handling before an incident occurs.

> Confidence: The dependency concern itself is Verified. The account ID and region for `cloudox-demo-atlas-prod-pg` are not confirmed in this package (the resource appears without an account or region), so the exact hosting context of the instance should be validated with the environment owner.

### Shared Dependencies

No shared dependency evidence beyond the `cloudox-demo-atlas-prod-pg` datastore is present in this section's package. Other resource categories (networking, observability integrations, etc.) are covered in their respective sections of this view.

One material tagging gap affects classification confidence across the environment: **781 resources have no Environment / Stage / Tier tag** and rely on inference for workload assignment. This means the full blast radius of a shared dependency failure may be underestimated until tagging is improved.

### Changes Since Previous Snapshot

Several infrastructure changes were observed between the previous snapshot (2026-07-20T11:50:27Z) and the current one (2026-07-20T12:54:55Z) that are relevant to the operational picture of critical components:

- **New DR CloudFormation stack** (`StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536`) was added in us-east-1 under account `122122642149` — this is the same account as the Atlas Prod workload and may represent a new disaster-recovery deployment worth verifying against the `cloudox-demo-atlas-prod-pg` resilience gap.
- **New CloudWatch Alarm** `cloudox-demo-atlas-prod-dr-bucket-size` was added in us-east-1 (`122122642149`), suggesting DR bucket monitoring is now in place for the prod workload.
- **Three services entered discovered scope**: `securityhub`, `ssm`, and `stepfunctions` — their relationship to critical workloads is not established in this package.
- **New DynamoDB table** `cloudox-demo-sandbox-events` (`arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-events`) was added in the sandbox account.
- **New EIP** `cloudox-demo-atlas-dev-nat-eip` (`eipalloc-083eada77de5498db`) was added in account `105769365151` eu-central-1, and a new **network interface** `eni-058ad447b7287912a` appeared in account `122122642149` eu-central-1.
- Two workloads were inferred to have grown: **Cloudox** (9 → 10 resources) and **Cloudox Demo Atlas Dev** (4 → 5 resources).

An additional **70 changes** were recorded in this snapshot period but are not enumerated here. See the Environment Evolution page for the full list.

![Workload architecture](./diagrams/operations-workload-architecture.png)

> **Figure — Workload architecture.** What are the significant workloads and how do their tiers connect? Scope: operations view · critical components. 4 of 5 inferred workload(s) shown (most significant first) with ingress → compute → data tiers. Edges are drawn only where a graph relationship exists. 1 additional workload(s) omitted to keep the diagram readable.
