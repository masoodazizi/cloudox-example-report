# FinOps View — Optimization Opportunities

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Likely_

## Optimization Opportunities

Two candidates have been identified — one cost-focused, one architectural — both requiring validation before any action is taken. Neither carries a precise dollar figure (cost attribution should be confirmed in AWS Cost Explorer), but both represent patterns known to generate ongoing charges or availability risk.

### Optimization Candidates

**NAT Gateway Consolidation** (Cost domain · Confidence: Likely)

One NAT Gateway — `cloudox-demo-atlas-dev-nat` (`nat-05bf82584b9610324`, account `105769365151`, eu-central-1) — carries non-production tags. NAT Gateways bill both per hour and per GB of data processed, meaning even lightly used gateways accumulate a fixed hourly cost continuously. A dedicated Elastic IP (`eipalloc-083eada77de5498db`) is also associated with this gateway, adding a small but persistent charge.

For non-production environments, options worth validating include: consolidating to a shared egress path, replacing NAT Gateway routes with VPC endpoints for AWS-service traffic, or removing the gateway entirely if outbound internet access is not required in that environment. **Validation is required** — egress patterns and any HA requirements must be confirmed before changes are made.

**Single-AZ Datastore: cloudox-demo-atlas-prod-pg** (Architecture domain · Confidence: Verified)

The RDS instance `cloudox-demo-atlas-prod-pg` has no Multi-AZ standby configured. This is primarily an availability and recovery posture concern: a zone failure could cause downtime or data loss. From a FinOps perspective, enabling Multi-AZ would increase RDS cost (roughly doubling instance-hour charges for that instance), so any decision to remediate should weigh the cost increase against the recovery requirement. The recommended action is to evaluate whether a Multi-AZ standby is appropriate given the production workload's RTO/RPO targets.

### Estimated Impact

Neither opportunity carries a package-derived dollar estimate. Spend figures are not attributed at the resource level in this section; exact costs for the NAT Gateway (hourly + data processing) and the RDS instance should be pulled from AWS Cost Explorer or CUR, filtered to the relevant account and resource.

Material gaps that limit optimization coverage:
- CloudWatch utilization metrics are not collected in this version, so idle-resource and right-sizing recommendations are not available.
- RDS provisioned IOPS, DynamoDB capacity mode, and S3 storage classes are outside current collector scope.
- Only 1% of resources carry a cost-allocation tag, making tag-based attribution unreliable across the environment.
- Approximately 22% of spend maps to services not yet linked to discovered architecture and is reported as unassociated.

These gaps mean the two candidates above represent a floor, not a ceiling, of optimization potential.

### Changes Since Previous Snapshot

Several changes since the previous snapshot (approximately one hour prior) are directly relevant to the NAT Gateway opportunity:

- The NAT Gateway `cloudox-demo-atlas-dev-nat` (`nat-05bf82584b9610324`) and its associated Elastic IP (`eipalloc-083eada77de5498db`, `cloudox-demo-atlas-dev-nat-eip`) were **newly added** in this snapshot — meaning the per-hour billing clock for this resource started recently.
- A new DynamoDB table `cloudox-demo-sandbox-events` was added in account `161388682021` (eu-central-1), which may introduce additional read/write capacity charges depending on its provisioned or on-demand mode (capacity mode is not captured by current collectors).
- A CloudFormation StackSet stack for a prod DR configuration (`StackSet-cloudox-demo-workload-prod-dr-…`, us-east-1) and an associated CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`) were added, suggesting DR infrastructure expansion in us-east-1 that may carry its own cost footprint.
- Services `securityhub`, `ssm`, and `stepfunctions` entered the discovered scope; their cost contribution is not attributed in this section.
- Two workloads (`cloudox` and `cloudox-demo-atlas-dev`) tentatively grew by one member resource each.

Note: 88 additional changes occurred in this snapshot period and are not enumerated here — see the Environment Evolution page for the full list.
