# Generic View — Unknowns & Validation Questions

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Likely_

## Unknowns & Validation Questions

Two categories of open items shape how much confidence to place in the rest of this view: evidence gaps that limit what CloudoX could determine automatically, and validation questions that require a human to confirm intent. Neither category implies a problem — they mark the boundary between what is known and what still needs an owner.

### What We Couldn't Confirm

The following gaps are structural limitations of the current discovery run. They do not indicate misconfiguration, but they do constrain the reliability of cost analysis and resource classification.

**Tagging coverage is too low for reliable cost allocation.**  
Only 1% of resources carry a configured cost-allocation tag. As a result, tag-based cost attribution across workloads, environments, or teams is not yet meaningful. 781 resources also have no Environment / Stage / Tier tag and rely on inference for classification — those classifications should be treated as approximate.
*(Confidence: Unknown — based on tag inventory from the current snapshot)*

**~22% of spend cannot be mapped to discovered architecture.**  
About 22% of total spend falls in services that CloudoX does not currently map to the discovered resource graph. This portion is reported as unassociated rather than attributed to a workload. The cost picture for mapped workloads is therefore incomplete by that margin.
*(Confidence: Unknown)*

**No utilization metrics are available.**  
CloudoX does not collect CloudWatch utilization metrics in this version. Idle resource detection, underutilization flags, and right-sizing recommendations based on actual usage are not available. Any efficiency or optimization analysis in this view is based on configuration patterns, not observed load.
*(Confidence: Unknown)*

**Several resource attributes are outside collector scope.**  
RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes are not captured by the current collectors. Cost drivers and architectural details for these specific attributes are not detected and should be verified directly in the AWS console or Cost Explorer.
*(Confidence: Unknown)*

**AWS-wide breadth could not be cross-checked.**  
The Resource Explorer meta-collector was disabled or unavailable during this discovery run. CloudoX could not use it to cross-check whether all AWS-visible resources were reached. There may be resources in scope that are not reflected in this view.

---

### Questions to Validate

The following items require confirmation from a resource owner. They are flagged because CloudoX observed a pattern that warrants human review, but cannot determine intent from configuration alone.

**Does IAM role `cloudox-demo-sandbox-ci-admin` require its current privilege level, and who owns it?**  
This role (ID: `AROAAAAAAO5VMOEOZ70IX`) in account `161388682021` carries administrative privileges. The name suggests a CI/CD use case. Whether that privilege level is intentional and actively maintained — or a leftover from initial setup — is not determinable from the graph alone.
*(Confidence: Assumed — privilege level is observed; intent is not)*

**Does IAM role `cloudox-demo-sandbox-unused-admin` require its current privilege level, and who owns it?**  
This role (ID: `AROAAAAADPCL3BVEXUDTH`) in account `161388682021` also carries administrative privileges. The name includes "unused", which may indicate it is no longer active — but CloudoX cannot confirm last-use or intended lifecycle from available evidence.
*(Confidence: Assumed — privilege level is observed; activity and ownership are not)*
