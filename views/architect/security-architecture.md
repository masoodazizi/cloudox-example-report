# Architect View — Security Architecture

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Verified_

## Security Architecture

The environment carries a meaningful internet-facing surface area across both the Atlas prod and dev accounts, with three security groups confirmed open to the world and four public subnets routed through Internet Gateways. The IAM landscape spans 75 roles with no customer-managed policies observed — all permission boundaries are expressed through AWS-managed or inline policies. These two patterns together define the primary security architecture concerns for architects to reason about.

**Confidence: Verified** — derived from complete graph evidence for this domain.

### Trust & Identity

The environment uses a standard AWS Organizations cross-account trust model. Every member account carries an `OrganizationAccountAccessRole`, present and verified in accounts `122980216815` (`AROAAAAAB33DHRZ72LLFE`), `110019496666` (`AROAAAAACLQX0PDP245PQ`), `105769365151` (`AROAAAAAC6A0RHGHCRIWC`), `122122642149` (`AROAAAAADGV42TTJ3H83B`), and `161388682021` (`AROAAAAADE0WRYDYAJBXU`). These roles represent the primary break-glass / management-plane trust path from the management account and should be treated as high-privilege blast-radius assets.

The sandbox account (`161388682021`) holds two notable roles:
- **cloudox-demo-sandbox-ci-admin** (`AROAAAAAAO5VMOEOZ70IX`) — a CI pipeline admin role; its scope and trust policy warrant review to ensure it cannot escalate beyond the sandbox boundary.
- **cloudox-demo-sandbox-unused-admin** (`AROAAAAADPCL3BVEXUDTH`) — the name signals it may be dormant; an unused admin-scoped role is a standing privilege-escalation risk and a candidate for removal.

The Atlas dev account (`105769365151`) has a set of purpose-scoped workload roles: `cloudox-demo-atlas-dev-ecs-exec` (`AROAAAAADLCZISR8G4FBU`), `cloudox-demo-atlas-dev-ecs-task` (`AROAAAAADLX1PNSXHC8KC`), `cloudox-demo-atlas-dev-ingest-sfn` (`AROAAAAAAIJ1CH5G3USEC`), and `cloudox-demo-atlas-dev-lambda` (`AROAAAAACJ2F32IXZJ88A`). The separation of exec, task, Step Functions, and Lambda roles follows a least-privilege decomposition pattern.

The Atlas prod account (`122122642149`) includes `cloudox-demo-atlas-prod-backup` (`AROAAAAAADXSPE5ZZWXXZ`) and `cloudox-demo-atlas-prod-dr-replicator` (`AROAAAAACFUR6W6I0LR0G`), indicating backup and DR replication are modelled as distinct identities — a sound separation for auditability.

The management account (`110319895932`) holds `cloudox-demo-org-trail-logs` (`arn:aws:iam::110319895932:role/cloudox-demo-org-trail-logs`) supporting the org-level CloudTrail (`arn:aws:cloudtrail:eu-central-1:110319895932:trail/cloudox-demo-org-trail-o-aaaapzvebq`), and the CloudFormation StackSets service-linked role (`AWSServiceRoleForCloudFormationStackSetsOrgAdmin`), confirming centralized logging and org-wide stack deployment are in use.

The sandbox account also carries `cloudox-demo-sandbox-scratch-lambda` (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-scratch-lambda`) — a scratch/experimental role that should be scoped and time-bounded to prevent drift into production trust chains.

No customer-managed IAM policies are present in this section's evidence. All 75 roles rely on AWS-managed or inline policies, which limits the ability to audit and version permission boundaries centrally.

### Exposure & Boundaries

Three security groups are confirmed open to the internet (0.0.0.0/0 or ::/0 ingress rules), all in `eu-central-1`:

| Security Group | Account | Open Port | Ref |
|---|---|---|---|
| cloudox-demo-atlas-prod-sg-edge | 122122642149 | 443 | `sg-0459201826f8de5b3` |
| cloudox-demo-atlas-prod-sg-alb | 122122642149 | 80 | `sg-06f2b4190bf01d261` |
| cloudox-demo-atlas-dev-sg-ecs | 105769365151 | 80 | `sg-0d6a48061beb72eae` |

The prod edge group (`cloudox-demo-atlas-prod-sg-edge`) accepting 443 from the world is consistent with a public-facing TLS endpoint and is likely intentional. The ALB group (`cloudox-demo-atlas-prod-sg-alb`) accepting port 80 from the world warrants confirmation — if the ALB is expected to redirect HTTP to HTTPS, the 0.0.0.0/0 rule on 80 may be acceptable, but it should be explicitly validated rather than assumed. The dev ECS group (`cloudox-demo-atlas-dev-sg-ecs`) accepting port 80 from the world is the most architecturally concerning: ECS tasks in a dev environment should not typically be directly internet-reachable; this may indicate a missing ALB or WAF layer in front of the dev workload.

The load balancer `cloudox-demo-atlas-prod-alb` is confirmed internet-facing (`ref: cloudox-demo-atlas-prod-alb`). This is consistent with the prod edge and ALB security groups above. The recommended action is to confirm the scheme is required and that no unintended paths bypass the ALB to backend resources.

### Segmentation

Four public subnets are identified — two in the Atlas prod account and two in the Atlas dev account, all in `eu-central-1`. Each has a route table entry forwarding traffic to an Internet Gateway:

| Subnet Friendly Name | Account | Subnet ID |
|---|---|---|
| cloudox-demo-atlas-prod-public-a | 122122642149 | `subnet-013a24d318ed6f3d0` |
| cloudox-demo-atlas-prod-public-b | 122122642149 | `subnet-016c22941a019a137` |
| cloudox-demo-atlas-dev-public-a | 105769365151 | `subnet-065f522206524ab12` |
| cloudox-demo-atlas-dev-public-b | 105769365151 | `subnet-0f64d71c952a7898a` |

The presence of two public subnets per account (one per AZ) is consistent with a standard multi-AZ public/private VPC layout. The key architectural question is whether workloads placed in these subnets are intentionally public or whether they rely solely on security group rules for isolation — the latter is a weaker boundary. The dev ECS security group open on port 80 (noted above) suggests at least one workload in the dev public tier may not have a dedicated ingress layer in front of it.

Private subnet and NAT Gateway configuration is outside this section's scope and covered elsewhere in the view.

### Changes Since Previous Snapshot

Three IAM roles were added between the previous snapshot (2026-07-20T11:50) and the current one (2026-07-20T12:54):

- **cloudox-demo-sandbox-ci-admin** (`AROAAAAAAO5VMOEOZ70IX`, account `161388682021`) — a new CI admin role in the sandbox; given its admin scope, its trust policy and permission boundary should be reviewed promptly. (`arn:aws:iam::161388682021:role/cloudox-demo-sandbox-ci-admin`)
- **cloudox-demo-atlas-dev-ingest-sfn** (`AROAAAAAAIJ1CH5G3USEC`, account `105769365151`) — a new Step Functions execution role for the dev ingest pipeline. (`arn:aws:iam::105769365151:role/cloudox-demo-atlas-dev-ingest-sfn`)
- **cloudox-demo-atlas-prod-dr-replicator** (`AROAAAAACFUR6W6I0LR0G`, account `122122642149`) — a new DR replication role in the prod account; its cross-account trust targets and replication scope should be confirmed. (`arn:aws:iam::122122642149:role/cloudox-demo-atlas-prod-dr-replicator`)

Additional related changes exist beyond these three — see the Environment Evolution page for the full picture.
