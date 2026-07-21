# Security View — Governance & Coverage Gaps

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Verified_

## Governance & Coverage Gaps

Two collector-level blind spots mean the full breadth of resources across this AWS Organization cannot be confirmed — a material concern for Security & Governance teams who need complete inventory to assess exposure. The gaps documented here are evidence-quality limitations, not policy failures, but they directly constrain what can be asserted with confidence about coverage.

**Confidence: Verified** — derived from complete graph evidence for this domain.

### Org Guardrails

Seven accounts are in scope across this organization: **Management Account** (`110319895932`), **Sandbox Ma Account** (`161388682021`), **Workload Dev Account** (`105769365151`), **Workload Prod Account** (`122122642149`), **Log Archive Account** (`122980216815`, Confidence: Likely), **Audit Account** (`110019496666`, Confidence: Likely), and **Platform Account** (`150982215529`, Confidence: Likely).

An org-level CloudTrail trail (`arn:aws:cloudtrail:eu-central-1:747208208289103:trail/cloudox-demo-org-trail-o-aaaapzvebq`) is present in the Management Account, with a dedicated logging role (`arn:aws:iam::110319895932:role/cloudox-demo-org-trail-logs`). This provides a baseline audit log signal across the organization. A CloudFormation StackSets service-linked role (`arn:aws:iam::110319895932:role/aws-service-role/stacksets.cloudformation.amazonaws.com/AWSServiceRoleForCloudFormationStackSetsOrgAdmin`) is also present in the Management Account, indicating centrally managed stack deployments are in use.

In the **Sandbox Ma Account**, three IAM roles warrant governance attention:
- `cloudox-demo-sandbox-ci-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin`) — a CI role with admin-level naming; privilege scope should be validated.
- `cloudox-demo-sandbox-unused-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-unused-admin`) — the name explicitly signals this role may be unused; it should be reviewed for removal.
- `OrganizationAccountAccessRole` (`arn:aws:iam::161388682021:role/OrganizationAccountAccessRole`) — the standard cross-account access role created by AWS Organizations; its trust policy and usage should be confirmed as expected.
- `cloudox-demo-sandbox-scratch-lambda` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-scratch-lambda`) — a scratch/experimental Lambda execution role; confirm whether it is still needed.

Security groups are present in both the **Workload Prod Account** (`sg-0459201826f8de5b3`, `sg-06f2b4190bf01d261`) and the **Workload Dev Account** (`sg-0d6a48061beb72eae`). Detailed rule-level exposure analysis for these groups is covered in the network exposure sections of this view.

Tag-based governance is materially weak: **only 1% of resources carry a configured cost-allocation tag** (`evidence_gap:cost:only-1-of-resources-carry-a-configured-cost-allocation-tag-so-tag-based-cost-allocation-is-not-yet-reliable`). For Security & Governance teams, this means resource ownership, environment classification, and data-sensitivity tagging are likely equally sparse — making automated policy enforcement or scoped access controls based on tags unreliable at this time. **Confidence: Unknown** on the full tagging posture.

### Coverage Gaps

Two collector-level gaps directly limit the completeness of this security view. Both carry **Confidence: Unknown** and require resolution before the inventory can be treated as authoritative.

**1. Resource Explorer meta-collector was disabled or unavailable**
(`evidence_gap:coverage:resource-explorer-meta-collector-was-disabled-or-unavailable-aws-visible-breadth-could-not-be-cross-checked`)

AWS Resource Explorer provides a cross-account, cross-region index of all AWS-visible resources. Because this collector was unavailable, CloudoX could not cross-check its typed collector results against what AWS itself reports. Resources that exist in AWS but fall outside typed collector coverage may be entirely absent from this view. For governance teams, this means the inventory used to assess exposure and access cannot be confirmed as complete.

**Recommended action:** Enable Resource Explorer in the Management Account and re-run discovery to validate inventory breadth.

**2. Cloud Control meta-collector was disabled**
(`evidence_gap:coverage:cloud-control-meta-collector-was-disabled-long-tail-resource-types-are-limited-to-typed-collector-coverage`)

The Cloud Control API provides coverage for long-tail and newer AWS resource types that typed collectors do not explicitly handle. With this collector disabled, any such resource types in the environment are not represented in this view. This is a secondary gap relative to Resource Explorer but compounds the inventory completeness risk.

**Recommended action:** Enable the Cloud Control meta-collector and re-run discovery to capture long-tail resource types.

**Additional cost-domain gaps with governance relevance:**

While primarily cost-oriented, the following gaps also affect governance completeness — specifically the ability to understand what services are running and whether they are accounted for:

- **~22% of spend is unassociated** with discovered architecture (`evidence_gap:cost:about-22-of-spend-is-in-services-cloudox-does-not-map-to-discovered-architecture-it-is-reported-as-unassociated-rather-than-force-fit`). From a governance perspective, this means roughly one-fifth of active AWS spend corresponds to services or resources not yet visible in this inventory — a potential blind spot for access and exposure analysis. **Confidence: Unknown.**
- **RDS read replicas, RDS provisioned IOPS, DynamoDB capacity mode, Direct Connect, and S3 storage classes** are not captured by current collectors (`evidence_gap:cost:rds-read-replicas-rds-provisioned-iops-dynamodb-capacity-mode-direct-connect-and-s3-storage-classes-are-not-captured-by-the-current-collectors-so-cost-drivers-for-them-are-not-detected`). These resource sub-types may carry data or network exposure implications that are not currently surfaced. **Confidence: Unknown.**

For a complete picture of environment-wide changes, see the Environment Evolution page.
