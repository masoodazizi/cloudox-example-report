# Security View — Key Questions Answered

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Security View](./README.md) · Audience: Security & Governance teams · Confidence: Unknown_

## Key Questions Answered

### What is exposed to the internet, and to whom?

Nine internet-facing access paths are observed across the Workload Prod Account (`122122642149`) and Workload Dev Account (`105769365151`). The internet-facing load balancer `cloudox-demo-atlas-prod-alb` is the primary public entry point — its scheme is confirmed internet-facing. Backing it are two security groups in the Workload Prod Account with world-open ingress (`0.0.0.0/0` or `::/0`): `cloudox-demo-atlas-prod-sg-edge` (`sg-0459201826f8de5b3`, port 443) and `cloudox-demo-atlas-prod-sg-alb` (`sg-06f2b4190bf01d261`, port 80). In the Workload Dev Account, `cloudox-demo-atlas-dev-sg-ecs` (`sg-0d6a48061beb72eae`) also permits world-open ingress on port 80. Two public subnets — `cloudox-demo-atlas-prod-public-b` (`subnet-016c22941a019a137`, Workload Prod Account, eu-central-1) and `cloudox-demo-atlas-dev-public-b` (`subnet-0f64d71c952a7898a`, Workload Dev Account, eu-central-1) — have route tables forwarding traffic to an Internet Gateway, confirming direct internet reachability for resources placed in them.

All five of these exposures carry **Confidence: Verified** and severity **medium**. The recommended action for each is to confirm the exposure is intentional and restrict ingress or scheme if not.

### Where is identity or access over-privileged or unclear?

The package surfaces several IAM roles warranting scrutiny in the Sandbox Ma Account (`161388682021`). `cloudox-demo-sandbox-ci-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin`) and `cloudox-demo-sandbox-unused-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-unused-admin`) carry admin-level naming signals; the latter's name explicitly suggests it may be unused. `OrganizationAccountAccessRole` (`arn:aws:iam::161388682021:role/OrganizationAccountAccessRole`) is the standard cross-account break-glass role created by AWS Organizations — its trust policy and actual usage should be confirmed. In the Management Account (`110319895932`), `cloudox-demo-org-trail-logs` (`arn:aws:iam::110319895932:role/cloudox-demo-org-trail-logs`) is associated with the org-level CloudTrail trail, and `AWSServiceRoleForCloudFormationStackSetsOrgAdmin` (`arn:aws:iam::110319895932:role/aws-service-role/stacksets.cloudformation.amazonaws.com/AWSServiceRoleForCloudFormationStackSetsOrgAdmin`) is a service-linked role for StackSets org administration. The sandbox also contains `cloudox-demo-sandbox-scratch-lambda` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-scratch-lambda`), whose scope and current attachment are not confirmed in this package.

The environment-wide IAM picture covers 75 roles and 0 customer-managed policies. The absence of customer-managed policies means permission boundaries and fine-grained controls may rely entirely on AWS-managed or inline policies — this warrants validation.

**Confidence: Likely** for the over-privilege characterisation of the sandbox admin roles (based on naming and role presence; actual policy content is not confirmed in this package).

### What network access paths allow lateral movement?

The two public subnets (`subnet-016c22941a019a137` in Workload Prod, `subnet-0f64d71c952a7898a` in Workload Dev) route directly to Internet Gateways, meaning any resource placed in these subnets without a restrictive security group is internet-reachable. The world-open security groups on ports 80 and 443 in both environments extend this surface. Port 80 exposure on `cloudox-demo-atlas-prod-sg-alb` and `cloudox-demo-atlas-dev-sg-ecs` is particularly notable as unencrypted HTTP ingress from any source.

No evidence is present in this package regarding VPC peering, Transit Gateway, or inter-account routing paths that could enable lateral movement between accounts. Those network topology details are covered in other sections of this view.

**Confidence: Verified** for the direct internet paths described above.

### Where are governance or coverage gaps?

Two significant detective-control gaps affect 5 of the 6 scanned accounts:

- **GuardDuty** is enabled in only 1 of 6 scanned accounts. The five accounts without coverage are: Log Archive Account (`122980216815`), Workload Dev Account (`105769365151`), Workload Prod Account (`122122642149`), Management Account (`110319895932`), and Sandbox Ma Account (`161388682021`). This leaves threat detection absent from both workload environments and the management plane. **Confidence: Likely.**
- **IAM Access Analyzer** is enabled in only 1 of 6 scanned accounts, with the same five accounts uncovered. External-access analysis — the primary mechanism for detecting unintended resource sharing — is therefore absent from the majority of the estate. **Confidence: Likely.**

The recommended remediation for both is org-wide enablement via a delegated administrator, which would close coverage gaps centrally.

Additionally, 0 customer-managed IAM policies are observed. This may indicate reliance on AWS-managed policies without tailored permission boundaries, but confirmation from the environment owner is needed.

### What are the most important security findings?

Ranked by combined severity and breadth of impact:

| Priority | Finding | Severity | Accounts / Resources Affected |
|---|---|---|---|
| 1 | GuardDuty missing from 5 accounts | Medium | 122980216815, 105769365151, 122122642149, 110319895932, 161388682021 |
| 1 | IAM Access Analyzer missing from 5 accounts | Medium | 122980216815, 105769365151, 122122642149, 110319895932, 161388682021 |
| 2 | Internet-facing ALB (`cloudox-demo-atlas-prod-alb`) | Medium | Workload Prod Account |
| 2 | World-open SG port 443 (`cloudox-demo-atlas-prod-sg-edge`) | Medium | Workload Prod Account, eu-central-1 |
| 2 | World-open SG port 80 (`cloudox-demo-atlas-prod-sg-alb`) | Medium | Workload Prod Account, eu-central-1 |
| 2 | World-open SG port 80 (`cloudox-demo-atlas-dev-sg-ecs`) | Medium | Workload Dev Account, eu-central-1 |
| 2 | Public subnet with IGW route (`cloudox-demo-atlas-prod-public-b`) | Medium | Workload Prod Account, eu-central-1 |
| 2 | Public subnet with IGW route (`cloudox-demo-atlas-dev-public-b`) | Medium | Workload Dev Account, eu-central-1 |

No critical-severity findings are present in this package. The detective-control gaps (GuardDuty, Access Analyzer) are the highest-priority items because they reduce the organisation's ability to detect exploitation of the exposure findings.

### What security questions must be validated with the environment owner?

The following items require owner confirmation before conclusions can be drawn:

1. **Internet exposure intent** — Are the internet-facing ALB, world-open security groups (ports 80 and 443), and public subnets in both prod and dev intentional and documented? Port 80 (unencrypted HTTP) in particular should be confirmed or closed.
2. **Unused admin role** — Is `cloudox-demo-sandbox-unused-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-unused-admin`) actively used? If not, it should be removed.
3. **CI admin role scope** — What CI system assumes `cloudox-demo-sandbox-ci-admin` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin`), and is admin-level access required for its function?
4. **GuardDuty and Access Analyzer** — Is there a delegated administrator account (e.g., Audit Account `110019496666`) already configured for these services that was outside the scan scope, or are these services genuinely absent?
5. **Customer-managed policies** — Is the absence of customer-managed IAM policies intentional? Are permission boundaries applied through another mechanism?
6. **Log Archive Account classification** — The Log Archive Account (`122980216815`) carries **Confidence: Likely** on its account classification. The environment owner should confirm its role and whether it is in scope for GuardDuty and Access Analyzer enablement.
