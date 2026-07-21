# Generic View — Operations & Reliability

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Verified_

## Operations & Reliability

Discovery captured **833 resources** with no coverage gaps identified, giving a clean operational baseline for this snapshot. The single notable change since the previous snapshot is a reduction in internet exposure, described below.

### Reliability Signals

With 833 resources captured and zero coverage gaps reported, the discovery pass has full accounting across the resource types within scope. There are no reliability signals — such as missing redundancy markers, unhealthy endpoints, or misconfigured availability targets — surfaced in this section's package.

> **Confidence: Verified** — derived from complete graph evidence for this domain.

Two meta-collector gaps are worth noting for completeness: AWS Resource Explorer and Cloud Control were both unavailable during this run. This means long-tail or less-common resource types may not be represented, and the AWS-visible breadth of the environment could not be independently cross-checked. The 833-resource count reflects typed collector coverage only.

### Operational Concerns

No operational concerns are flagged in the current state. The coverage gap count is zero, meaning every resource captured falls within a monitored category.

The two disabled meta-collectors (Resource Explorer and Cloud Control) represent a process-level gap: if resource types exist that fall outside the typed collectors' scope, they would be invisible to this view. This is a known unknown rather than a confirmed problem, but it is worth re-enabling those collectors to validate completeness on the next run.

### Changes Since Previous Snapshot

Between the snapshot at **11:50 UTC** and the current snapshot at **12:54 UTC on 2026-07-20**, one exposure change was observed:

- **Security group `sg-04fae132cfc68e91d` is no longer reachable from the internet.** This is a provider-observed fact: the group's inbound internet path has been removed since the previous run.

Additional changes exist beyond this section's scope — see the **Environment Evolution** page for the full picture.
