# Environment Evolution

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

## Comparison

Scope: `organization=cloudox-demo`

| | Run id | Generated at | Resources | Relationships |
|---|---|---|---|---|
| Previous | run-20260720T115027Z | 2026-07-20T11:50:27.689953+00:00 | 9961 | 242 |
| Current | run-20260720T125455Z | 2026-07-20T12:54:55.508805+00:00 | 9989 | 260 |

## Summary of changes

| Category | Added | Removed | Modified |
|---|---|---|---|
| Resources | 32 | 4 | 29 |
| Relationships | 28 | 10 | - |
| Accounts | 0 | 0 | - |
| Regions | 0 | 0 | - |
| Services | 3 | 0 | - |
| Internet exposure | 1 | 1 | - |
| Workloads | 0 | 0 | - |

## Key observations

- Total resource count changed from 9961 to 9989 (+28). (Observed)
- New AWS service(s) observed: securityhub, ssm, stepfunctions. (Observed)
- 1 new internet-exposure indicator(s) detected. (Observed)
- 1 internet-exposure indicator(s) removed. (Observed)
- Workload 'cloudox' changed from 9 to 10 member resource(s). (Inferred)
- Workload 'cloudox-demo-atlas-dev' changed from 4 to 5 member resource(s). (Inferred)

## Scope changes

Services added: `securityhub`, `ssm`, `stepfunctions`

## Internet exposure changes

Newly exposed: `sg-0d6a48061beb72eae`

No longer exposed: `sg-04fae132cfc68e91d`

## Added resources

| Type | Identifier | Account | Region |
|---|---|---|---|
| AWS::IAM::RolePolicyAttachment | `AWSServiceRoleForSecurityHub:arn:aws:iam::aws:policy/aws-service-role/AWSSecurityHubServiceRolePolicy` | 110019496666 | - |
| AWS::IAM::Role | `AROAAAAACOCVK0V61HVJ0` | 110019496666 | - |
| AWS::SecurityHub::Hub | `default` | 110019496666 | eu-central-1 |
| AWS::IAM::Role | `AROAAAAAAIJ1CH5G3USEC` | 105769365151 | - |
| AWS::EC2::EIP | `eipalloc-083eada77de5498db` | 105769365151 | eu-central-1 |
| AWS::EC2::NatGateway | `nat-05bf82584b9610324` | 105769365151 | eu-central-1 |
| AWS::EC2::NetworkInterface | `eni-0dbafb51ea9a9ffd3` | 105769365151 | eu-central-1 |
| AWS::EC2::NetworkInterface | `eni-00815b97162c6a5fc` | 105769365151 | eu-central-1 |
| AWS::EC2::NetworkInterface | `eni-0d070d51a01905b0b` | 105769365151 | eu-central-1 |
| AWS::ECS::Task | `3869aa71501448f98b6ced83e40d4235` | 105769365151 | eu-central-1 |
| AWS::ECS::Task | `9c2924f01ffd402eaafca2bdf2d04d67` | 105769365151 | eu-central-1 |
| AWS::SQS::Queue | `arn:aws:sqs:eu-central-1:105769365151:cloudox-demo-atlas-dev-orders` | 105769365151 | eu-central-1 |
| AWS::SQS::Queue | `arn:aws:sqs:eu-central-1:105769365151:cloudox-demo-atlas-dev-orders-dlq` | 105769365151 | eu-central-1 |
| AWS::SSM::Parameter | `arn:aws:ssm:eu-central-1:105769365151:parameter/cloudox-demo/atlas/dev/feature-flags` | 105769365151 | eu-central-1 |
| AWS::StepFunctions::StateMachine | `arn:aws:states:eu-central-1:105769365151:stateMachine:cloudox-demo-atlas-dev-ingest` | 105769365151 | eu-central-1 |
| AWS::IAM::RoleInlinePolicy | `cloudox-demo-atlas-prod-dr-replicator:write-dr-snapshots` | 122122642149 | - |
| AWS::IAM::Role | `AROAAAAACFUR6W6I0LR0G` | 122122642149 | - |
| AWS::EC2::NetworkInterface | `eni-058ad447b7287912a` | 122122642149 | eu-central-1 |
| AWS::EC2::NetworkInterface | `eni-0fa5e7a1b3798b7d3` | 122122642149 | eu-central-1 |
| AWS::ECS::Task | `7aa166e18de34efd8699a663e13265f1` | 122122642149 | eu-central-1 |
| AWS::CloudFormation::Stack | `arn:aws:cloudformation:us-east-1:122122642149:stack/StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536/acb94bf7-b3da-c32a-1613-327a5c08def2` | 122122642149 | us-east-1 |
| AWS::CloudWatch::Alarm | `cloudox-demo-atlas-prod-dr-bucket-size` | 122122642149 | us-east-1 |
| AWS::ECR::Repository | `arn:aws:ecr:us-east-1:122122642149:repository/cloudox-demo/atlas-prod-dr/api` | 122122642149 | us-east-1 |
| AWS::KMS::Alias | `alias/cloudox-demo-atlas-prod-dr` | 122122642149 | us-east-1 |
| AWS::KMS::Key | `a55b807c-8e9a-9dd0-0ce8-85dc3f7a71b3` | 122122642149 | us-east-1 |
| AWS::Logs::LogGroup | `/cloudox/cloudox-demo/atlas-prod-dr/operations` | 122122642149 | us-east-1 |
| AWS::S3::Bucket | `cloudox-demo-atlas-prod-dr-snapshots-122122642149-us-east-1` | 122122642149 | us-east-1 |
| AWS::SNS::Topic | `arn:aws:sns:us-east-1:122122642149:cloudox-demo-atlas-prod-dr-ops-alerts` | 122122642149 | us-east-1 |
| AWS::Organizations::Policy | `p-v5y4enjq` | 110319895932 | - |
| AWS::IAM::RoleInlinePolicy | `cloudox-demo-sandbox-ci-admin:admin-everything` | 161388682021 | - |
| AWS::IAM::Role | `AROAAAAAAO5VMOEOZ70IX` | 161388682021 | - |
| AWS::DynamoDB::Table | `cloudox-demo-sandbox-events` | 161388682021 | eu-central-1 |

## Removed resources

| Type | Identifier | Account | Region |
|---|---|---|---|
| AWS::EC2::NetworkInterface | `eni-0596723d0b7500459` | 105769365151 | eu-central-1 |
| AWS::ECS::Task | `96f9f34fd1594394a800f7160c83bc59` | 105769365151 | eu-central-1 |
| AWS::EC2::NetworkInterface | `eni-0c9de7ff9cd35fe20` | 122122642149 | eu-central-1 |
| AWS::SNS::Topic | `arn:aws:sns:eu-central-1:161388682021:cloudox-demo-sandbox-orphan` | 161388682021 | eu-central-1 |

## Modified resources

| Type | Identifier | Changed fields |
|---|---|---|
| AWS::CloudFormation::Stack | `arn:aws:cloudformation:eu-central-1:122980216815:stack/StackSet-cloudox-demo-log-archive-f1d5f651-ffb0-d6df-6add-0bf1786d5986/5e12c5b4-b144-40f2-315e-3cf08775ce0e` | last_updated_time: 2026-05-20T10:52:56.406000+00:00 → 2026-07-20T12:01:12.869000+00:00 |
| AWS::S3::Bucket | `cloudox-demo-access-logs-122980216815-eu-central-1` | changed:encryption, changed:lifecycle, changed:public_access_block, changed:versioning |
| AWS::S3::Bucket | `cloudox-demo-cloudtrail-122980216815-eu-central-1` | creation_date: 2026-05-19T13:47:24+00:00 → 2026-07-20T12:01:20+00:00, changed:encryption, changed:lifecycle, changed:policy_status, changed:public_access_block, changed:versioning |
| AWS::S3::Bucket | `cloudox-demo-config-122980216815-eu-central-1` | creation_date: 2026-05-19T13:47:24+00:00 → 2026-07-20T12:01:19+00:00, changed:encryption, changed:lifecycle, changed:policy_status, changed:public_access_block, changed:versioning |
| AWS::IAM::ManagedPolicy | `ANPAJQPCESDDYDLLSOGYO` | attachment_count: 0 → 1 |
| AWS::CloudFormation::Stack | `arn:aws:cloudformation:eu-central-1:110019496666:stack/StackSet-cloudox-demo-audit-e4e271f9-fc6d-22b1-e7f6-b8d181fd9b67/35575010-ce4d-ca1d-3b73-45ad60156ccb` | last_updated_time: ∅ → 2026-07-20T12:02:11.911000+00:00, stack_status: CREATE_COMPLETE → UPDATE_COMPLETE |
| AWS::GuardDuty::Detector | `d7e20c569a3f438bb45236e9c2fcbcfa` | changed:features |
| AWS::CloudFormation::Stack | `arn:aws:cloudformation:eu-central-1:105769365151:stack/StackSet-cloudox-demo-workload-dev-1cca67c9-ed1b-c179-854f-4cd4d1711ecd/d3322a31-9b0c-1fc0-59fe-baa7bcf5f5c5` | last_updated_time: 2026-07-20T11:32:05.858000+00:00 → 2026-07-20T12:02:47.929000+00:00 |
| AWS::EC2::RouteTable | `rtb-009215bffcfd9f9a0` | changed:associations, changed:routes |
| AWS::EC2::SecurityGroup | `sg-0d6a48061beb72eae` | changed:ip_permissions |
| AWS::EC2::Subnet | `subnet-065f522206524ab12` | available_ip_address_count: 251 → 249 |
| AWS::ECS::Cluster | `cloudox-demo-atlas-dev` | running_tasks_count: 1 → 2 |
| AWS::ECS::Service | `cloudox-demo-atlas-dev-api` | desired_count: 1 → 2, running_count: 1 → 2 |
| AWS::S3::Bucket | `cloudox-demo-atlas-dev-app-105769365151-eu-central-1` | changed:encryption, changed:lifecycle, changed:public_access_block, changed:versioning |
| AWS::CloudFormation::Stack | `arn:aws:cloudformation:eu-central-1:122122642149:stack/StackSet-cloudox-demo-workload-prod-3fa47485-72dc-52dd-4672-e5243b802465/a542738f-45f9-2c39-27e8-c59ee41f400a` | last_updated_time: ∅ → 2026-07-20T12:05:31.086000+00:00, stack_status: CREATE_COMPLETE → UPDATE_COMPLETE |
| AWS::CloudWatch::Alarm | `cloudox-demo-atlas-prod-ecs-running-task-low` | threshold: 1.0 → 2.0 |
| AWS::EC2::RouteTable | `rtb-01181117bc9ca1e3e` | changed:associations |
| AWS::EC2::Subnet | `subnet-013a24d318ed6f3d0` | available_ip_address_count: 250 → 249 |
| AWS::ECS::Cluster | `cloudox-demo-atlas-prod` | running_tasks_count: 1 → 2 |
| AWS::ECS::Service | `cloudox-demo-atlas-prod-api` | desired_count: 1 → 2, running_count: 1 → 2 |
| AWS::RDS::DBInstance | `cloudox-demo-atlas-prod-pg` | instance_class: db.t4g.micro → db.t4g.small |
| AWS::S3::Bucket | `cloudox-demo-atlas-prod-app-122122642149-eu-central-1` | changed:encryption, changed:lifecycle, changed:public_access_block, changed:versioning |
| AWS::CloudFormation::Stack | `arn:aws:cloudformation:eu-central-1:110319895932:stack/cloudox-demo-management-baseline/fec94f2f-f9f2-b1ef-b656-e283b7637737` | last_updated_time: 2026-07-20T11:34:59.166000+00:00 → 2026-07-20T12:15:57.109000+00:00 |
| AWS::S3::Bucket | `cloudox-cust-sandbox-a8d4` | changed:encryption, changed:public_access_block, changed:versioning |
| AWS::S3::Bucket | `cloudox-public-example` | changed:encryption, changed:public_access_block, changed:versioning |
| AWS::CloudFormation::Stack | `arn:aws:cloudformation:eu-central-1:161388682021:stack/StackSet-cloudox-demo-sandbox-ma-38e3afc3-b450-1d3e-ea02-c3262b9d6adf/10fedcdb-f531-d312-a121-9a8d1966bac1` | last_updated_time: 2026-07-20T11:33:11.515000+00:00 → 2026-07-20T12:14:22.970000+00:00 |
| AWS::EC2::SecurityGroup | `sg-04fae132cfc68e91d` | changed:ip_permissions |
| AWS::S3::Bucket | `cloudox-demo-sandbox-161388682021-eu-central-1` | changed:encryption, changed:public_access_block, changed:versioning |
| AWS::S3::Bucket | `cloudox-demo-sandbox-abandoned-161388682021-eu-central-1` | changed:encryption, changed:public_access_block, changed:versioning |

## New relationships

| Type | Source | Target |
|---|---|---|
| attached_to | `eipalloc-083eada77de5498db` | `eni-0dbafb51ea9a9ffd3` |
| depends_on | `3869aa71501448f98b6ced83e40d4235` | `cloudox-demo-atlas-dev-api:1` |
| depends_on | `7aa166e18de34efd8699a663e13265f1` | `cloudox-demo-atlas-prod-api:2` |
| depends_on | `9c2924f01ffd402eaafca2bdf2d04d67` | `cloudox-demo-atlas-dev-api:1` |
| exposed_to_internet | `internet` | `sg-0d6a48061beb72eae` |
| in_subnet | `eni-058ad447b7287912a` | `subnet-0b0384769d442046a` |
| in_subnet | `eni-0dbafb51ea9a9ffd3` | `subnet-065f522206524ab12` |
| in_subnet | `eni-0fa5e7a1b3798b7d3` | `subnet-013a24d318ed6f3d0` |
| in_subnet | `eni-00815b97162c6a5fc` | `subnet-065f522206524ab12` |
| in_subnet | `eni-0d070d51a01905b0b` | `subnet-0f64d71c952a7898a` |
| in_subnet | `nat-05bf82584b9610324` | `subnet-065f522206524ab12` |
| in_vpc | `eni-058ad447b7287912a` | `vpc-0aaa6d4f2f981e945` |
| in_vpc | `eni-0dbafb51ea9a9ffd3` | `vpc-0a4d44a07f48d7ca0` |
| in_vpc | `eni-0fa5e7a1b3798b7d3` | `vpc-0aaa6d4f2f981e945` |
| in_vpc | `eni-00815b97162c6a5fc` | `vpc-0a4d44a07f48d7ca0` |
| in_vpc | `eni-0d070d51a01905b0b` | `vpc-0a4d44a07f48d7ca0` |
| in_vpc | `nat-05bf82584b9610324` | `vpc-0a4d44a07f48d7ca0` |
| part_of | `3869aa71501448f98b6ced83e40d4235` | `cloudox-demo-atlas-dev` |
| part_of | `7aa166e18de34efd8699a663e13265f1` | `cloudox-demo-atlas-prod` |
| part_of | `9c2924f01ffd402eaafca2bdf2d04d67` | `cloudox-demo-atlas-dev` |
| part_of_workload | `3869aa71501448f98b6ced83e40d4235` | `cloudox-demo-atlas-dev` |
| part_of_workload | `7aa166e18de34efd8699a663e13265f1` | `cloudox` |
| part_of_workload | `9c2924f01ffd402eaafca2bdf2d04d67` | `cloudox-demo-atlas-dev` |
| routes_to | `rtb-009215bffcfd9f9a0` | `nat-05bf82584b9610324` |
| secured_by | `eni-058ad447b7287912a` | `sg-0831f3eae117601d2` |
| secured_by | `eni-0fa5e7a1b3798b7d3` | `sg-0457a284b8b9ead6c` |
| secured_by | `eni-00815b97162c6a5fc` | `sg-0d6a48061beb72eae` |
| secured_by | `eni-0d070d51a01905b0b` | `sg-0d6a48061beb72eae` |

## Removed relationships

| Type | Source | Target |
|---|---|---|
| depends_on | `96f9f34fd1594394a800f7160c83bc59` | `cloudox-demo-atlas-dev-api:1` |
| exposed_to_internet | `internet` | `sg-04fae132cfc68e91d` |
| in_subnet | `eni-0c9de7ff9cd35fe20` | `subnet-0b0384769d442046a` |
| in_subnet | `eni-0596723d0b7500459` | `subnet-0f64d71c952a7898a` |
| in_vpc | `eni-0c9de7ff9cd35fe20` | `vpc-0aaa6d4f2f981e945` |
| in_vpc | `eni-0596723d0b7500459` | `vpc-0a4d44a07f48d7ca0` |
| part_of | `96f9f34fd1594394a800f7160c83bc59` | `cloudox-demo-atlas-dev` |
| part_of_workload | `96f9f34fd1594394a800f7160c83bc59` | `cloudox-demo-atlas-dev` |
| secured_by | `eni-0c9de7ff9cd35fe20` | `sg-0831f3eae117601d2` |
| secured_by | `eni-0596723d0b7500459` | `sg-0d6a48061beb72eae` |

## Workload changes (Inferred)

Resized workloads:

- `cloudox`: 9 → 10 member resources
- `cloudox-demo-atlas-dev`: 4 → 5 member resources

## Limitations

- Resource matching uses AWS-native keys (ARN or native id). A resource recreated with a new identifier appears as a removal plus an addition rather than a modification.
- Modified-resource detection compares normalised top-level properties and tags; semantically equivalent representations may surface as modifications.
- Workload, system, and exposure groupings are heuristic interpretations; changes may reflect interpreter updates rather than infrastructure change.
