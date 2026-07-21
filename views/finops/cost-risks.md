# FinOps View — Cost Risks

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [FinOps View](./README.md) · Audience: FinOps / Finance · Confidence: Likely_

## Cost Risks

The most material cost risks in this environment are not overspend on a single service — they are structural gaps that limit visibility and control: only 1% of resources carry a cost-allocation tag, roughly 22% of spend cannot be mapped to discovered architecture, and several key AWS cost-governance services are absent from most accounts. These gaps make it difficult to attribute spend, detect anomalies early, or enforce budgets reliably.

### Cost & Commitment Risks

Four findings shape the cost-risk picture for this environment:

**Security service gaps with cost-governance implications**

GuardDuty threat detection and IAM Access Analyzer are each enabled in only 1 of 6 scanned accounts, and are missing from 5 accounts: Log Archive Account (`122980216815`), Workload Dev Account (`105769365151`), Workload Prod Account (`122122642149`), Management Account (`110319895932`), and Sandbox Ma Account (`161388682021`). From a FinOps perspective, the absence of these controls is a cost risk as well as a security risk: undetected compromised credentials or exfiltration events can generate unexpected, large, and difficult-to-dispute charges. GuardDuty and IAM Access Analyzer both carry their own service costs when enabled, but those costs are modest relative to the exposure of running without them. Enabling them org-wide via a delegated administrator is the recommended path. Confidence: Likely.

**Broadly-privileged IAM roles in the Sandbox account**

Two IAM roles in Sandbox Ma Account (`161388682021`) — `cloudox-demo-sandbox-ci-admin` and `cloudox-demo-sandbox-unused-admin` — are inferred from naming to carry broad administrative privileges. If either role is misused or compromised, it could provision resources that generate unbudgeted spend. The role named `cloudox-demo-sandbox-unused-admin` is particularly notable: the name suggests it may not be actively needed, making it a candidate for removal. Privilege breadth has not been directly collected, so these findings require validation against the attached policies before action. Confidence: Assumed — validate before acting.

**Tag coverage is too low for reliable cost allocation**

Only 1% of resources carry a configured cost-allocation tag. This means tag-based showback, chargeback, or per-workload budget tracking is not currently viable. Improving tag coverage is a prerequisite for any meaningful FinOps maturity improvement in this environment.

**Unassociated spend**

Approximately 22% of spend is in services CloudoX does not map to discovered architecture. This spend is reported as unassociated rather than force-fit to a workload. Until tag coverage improves or those services are brought into scope, this portion of the bill cannot be attributed or governed architecturally.

> **What is not covered here:** CloudWatch utilization metrics are not collected in this version, so idle-resource and right-sizing risks are not assessed. RDS, DynamoDB capacity mode, Direct Connect, and S3 storage-class cost drivers are also outside current collector scope. These gaps are material and should be factored into any optimization roadmap.
