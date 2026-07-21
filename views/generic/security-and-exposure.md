# Generic View — Security & Exposure

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Verified_

## Security & Exposure

The most immediate concern in this environment is the combination of world-open network ingress on production and development workloads, and the absence of GuardDuty and IAM Access Analyzer coverage across the majority of accounts. Both gaps are medium-severity findings that affect 5 of the 6 scanned accounts. The environment holds 75 IAM roles, 3 security groups open to the internet, 15 VPCs, 63 subnets, 1 load balancer, and 9 observed internet-facing access paths.

**Confidence: Verified** — derived from complete graph evidence for this domain.

---

### Internet Exposure

Three security groups and one load balancer present confirmed internet-facing exposure, all in `eu-central-1`.

| Resource | Account | Open Port(s) | Ref |
|---|---|---|---|
| cloudox-demo-atlas-prod-sg-edge | Workload Prod Account (122122642149) | 443 (0.0.0.0/0, ::/0) | `sg-0459201826f8de5b3` |
| cloudox-demo-atlas-prod-sg-alb | Workload Prod Account (122122642149) | 80 (0.0.0.0/0, ::/0) | `sg-06f2b4190bf01d261` |
| cloudox-demo-atlas-dev-sg-ecs | Workload Dev Account (105769365151) | 80 (0.0.0.0/0, ::/0) | `sg-0d6a48061beb72eae` |

The load balancer `cloudox-demo-atlas-prod-alb` is configured with an internet-facing scheme, contributing directly to the reachable surface area on the production side. The recommended action for each of these is to confirm the exposure is intentional; if not, restrict the ingress rules or change the load balancer scheme.

The dev-tier ECS security group (`cloudox-demo-atlas-dev-sg-ecs`) accepting world-open HTTP on port 80 is worth particular attention — unrestricted ingress to a development workload is a common path for unintended exposure.

---

### Identity & Access

The environment contains 75 IAM roles and 0 customer-managed policies. Several key roles are highlighted below.

**Sandbox Account (161388682021)**
- `cloudox-demo-sandbox-ci-admin` (`AROAAAAAAO5VMOEOZ70IX`) — a CI-oriented admin role.
- `cloudox-demo-sandbox-unused-admin` (`AROAAAAADPCL3BVEXUDTH`) — an admin-level role whose name suggests it may be inactive; worth reviewing for removal.
- `OrganizationAccountAccessRole` (`AROAAAAADE0WRYDYAJBXU`) — the standard cross-account management role.

**Workload Dev Account (105769365151)**
- `cloudox-demo-atlas-dev-ingest-sfn` (`AROAAAAAAIJ1CH5G3USEC`) — Step Functions execution role for the ingest pipeline.
- `cloudox-demo-atlas-dev-ecs-exec` (`AROAAAAADLCZISR8G4FBU`) and `cloudox-demo-atlas-dev-ecs-task` (`AROAAAAADLX1PNSXHC8KC`) — ECS execution and task roles.
- `cloudox-demo-atlas-dev-lambda` (`AROAAAAACJ2F32IXZJ88A`) — Lambda execution role.

**Workload Prod Account (122122642149)**
- `cloudox-demo-atlas-prod-backup` (`AROAAAAAADXSPE5ZZWXXZ`) — backup service role.

`OrganizationAccountAccessRole` instances are present across Log Archive (122980216815), an additional account (110019496666), Workload Dev (105769365151), Workload Prod (122122642149), and Sandbox (161388682021) — these provide management-account cross-account access and should be treated as high-privilege paths.

No customer-managed IAM policies were found in the scanned scope; all role permissions appear to be delivered via AWS-managed or inline policies, which limits centralised policy governance visibility.

---

### Notable Risks

**Uneven GuardDuty coverage — medium severity**
GuardDuty threat detection is enabled in only 1 of 6 scanned accounts. The five accounts without coverage are: Log Archive (122980216815), Workload Dev (105769365151), Workload Prod (122122642149), Management (110319895932), and Sandbox (161388682021). This leaves the majority of the environment — including production and the management account — without active threat detection. The recommended path is to enable GuardDuty org-wide via a delegated administrator. *(Confidence: Likely — Log Archive account coverage status is inferred.)*

**Uneven IAM Access Analyzer coverage — medium severity**
IAM Access Analyzer is enabled in only 1 of 6 scanned accounts, with the same five accounts uncovered as GuardDuty. Without Access Analyzer, externally accessible IAM resources (roles, S3 buckets, KMS keys, etc.) in those accounts will not be automatically flagged. Enabling it org-wide via a delegated administrator is the recommended remediation. *(Confidence: Likely — Log Archive account coverage status is inferred.)*

Together, these two gaps mean that 5 accounts — including the Management Account and both workload accounts — currently lack both automated threat detection and external-access analysis.

---

### Changes Since Previous Snapshot

Between the snapshot at 11:50 UTC and the current snapshot at 12:54 UTC on 2026-07-20, four security-relevant changes were observed:

- **New internet exposure:** `cloudox-demo-atlas-dev-sg-ecs` (`sg-0d6a48061beb72eae`) became reachable from the internet — this is the world-open port-80 finding described above.
- **Exposure removed:** A previously internet-reachable security group (`sg-04fae132cfc68e91d`) is no longer reachable from the internet.
- **New IAM roles added:** `cloudox-demo-sandbox-ci-admin` (`AROAAAAAAO5VMOEOZ70IX`) was added to the Sandbox Account, and `cloudox-demo-atlas-dev-ingest-sfn` (`AROAAAAAAIJ1CH5G3USEC`) was added to the Workload Dev Account.

The net effect on exposure is neutral (one path opened, one closed), but the newly exposed dev ECS security group warrants prompt review. Additional related changes exist — see the Environment Evolution page for the full picture.
