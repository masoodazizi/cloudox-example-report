# Operations View — Operational Overview

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Operations View](./README.md) · Audience: Platform / Operations Engineers · Confidence: Likely_

## Operational Overview

The environment spans **833 resources across 6 of 7 known accounts** and **2 regions** (eu-central-1 and us-east-1). Four significant workloads are active alongside one governance/platform system; one additional workload has been demoted to helper/governance status. The majority of resource classification relies on inference rather than explicit tagging — 781 resources carry no Environment/Stage/Tier tag — which is the primary operational risk for triage and blast-radius assessment.

### What's Running

Resources are distributed across the following accounts and environments:

| Account | Friendly Name | Environment |
|---|---|---|
| 110319895932 | Management Account | — |
| 161388682021 | Sandbox Ma Account | Sandbox Environment |
| 105769365151 | Workload Dev Account | Development Environment |
| 122122642149 | Workload Prod Account | Production Environment (122122642149) |
| 122980216815 | Log Archive Account | 122980216815 Unknown |
| 110019496666 | Audit Account | — |
| 150982215529 | Platform Account | Production Environment (150982215529) |

One account is excluded from scope and is not reflected in the 833-resource count.

Key data-plane resources confirmed in scope include:
- **DynamoDB tables**: `cloudox-demo-atlas-dev-items` (Workload Dev Account, `arn:aws:dynamodb:eu-central-1:105769365151:table/cloudox-demo-atlas-dev-items`), `cloudox-demo-atlas-prod-items` (Workload Prod Account, `arn:aws:dynamodb:eu-central-1:122122642149:table/cloudox-demo-atlas-prod-items`), `cloudox-demo-sandbox-events` (Sandbox Ma Account, `arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-events`), and `cloudox-demo-sandbox-scratch` (Sandbox Ma Account, `arn:aws:dynamodb:eu-central-1:161388682021:table/cloudox-demo-sandbox-scratch`).
- **API Gateway endpoints**: `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com` and `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com` — both internet-facing in eu-central-1.
- **Internet Gateways**: present in the Audit Account (`igw-00ed21b9a0e6596a8` in us-east-1, `igw-0d14f1dd4e54d5906` in eu-central-1) and in the Workload Dev Account (`igw-0567575921f471548` in us-east-1), confirming public egress/ingress paths in multiple accounts and regions.
- **Services newly in scope**: Security Hub (`securityhub`), Systems Manager (`ssm`), and Step Functions (`stepfunctions`) have entered the discovered scope since the previous snapshot.

> **Coverage gap**: Resource Explorer and Cloud Control meta-collectors were unavailable during this discovery run. Long-tail resource types and the full AWS-visible breadth of the environment could not be cross-checked. The 833-resource figure should be treated as a lower bound.

### Operational Footprint

**Accounts and regions**: The active workload footprint spans eu-central-1 (primary) and us-east-1 (DR/secondary), with internet gateways confirmed in both regions across multiple accounts. The Audit Account (`110019496666`) holds internet gateways in both regions, which warrants verification that this is intentional.

**Internet exposure**: Two security groups have changed exposure state (see Changes below). Security group `sg-0d6a48061beb72eae` is now reachable from the internet; `sg-04fae132cfc68e91d` is no longer reachable. The two API Gateway endpoints (`https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`, `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`) are confirmed internet-accessible.

**DR posture**: A new CloudFormation StackSet stack (`StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536`, `arn:aws:cloudformation:us-east-1:122122642149:stack/StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536/acb94bf7-b3da-c32a-1613-327a5c08def2`) has been deployed in the Workload Prod Account in us-east-1, consistent with a DR build-out. A corresponding CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`, `arn:aws:cloudwatch:us-east-1:122122642149:alarm:cloudox-demo-atlas-prod-dr-bucket-size`) monitors DR bucket size in the same account and region.

**Observability**: Security Hub and SSM are now in scope, which may indicate new compliance or patch-management coverage. The CloudWatch alarm for the DR bucket is the only alarm explicitly observed in this section's evidence set; broader alarm and dashboard coverage is not confirmed here.

**Tagging and classification risk**: 781 of 833 resources (≈94%) carry no Environment/Stage/Tier tag. Workload and environment assignments for these resources are inferred, not authoritative. This directly affects incident triage, change-window scoping, and cost attribution. Operators should treat any automated classification of untagged resources as tentative.

**Log Archive Account**: The Log Archive Account (`122980216815`) has a confidence of Likely and its environment classification is Assumed. Confirm that log delivery pipelines to this account are intact and that the account is not inadvertently excluded from future discovery runs.

### Changes Since Previous Snapshot

The following changes were observed between the previous snapshot (2026-07-20T11:50:27Z) and the current run (2026-07-20T12:54:55Z):

- **Exposure change (action required)**: Security group `sg-0d6a48061beb72eae` became reachable from the internet. Validate that this is intentional and that the associated resources are appropriately hardened.
- **Exposure change**: Security group `sg-04fae132cfc68e91d` is no longer reachable from the internet. Confirm this is expected and not an unintended connectivity break.
- **New services in scope**: `securityhub`, `ssm`, and `stepfunctions` entered the discovered scope — verify these are enabled and configured as intended across the relevant accounts.
- **DR stack deployed**: CloudFormation stack `StackSet-cloudox-demo-workload-prod-dr-e27aba00-144f-1560-0aed-593e2d919536` was added in the Workload Prod Account (us-east-1), alongside the CloudWatch alarm `cloudox-demo-atlas-prod-dr-bucket-size`.
- **New DynamoDB table**: `cloudox-demo-sandbox-events` was added in the Sandbox Ma Account (eu-central-1).
- **Workload sizing (inferred)**: The `cloudox` workload tentatively grew from 9 to 10 member resources; `cloudox-demo-atlas-dev` tentatively grew from 4 to 5 member resources.

An additional **72 changes** occurred in this snapshot period that are not enumerated here. See the Environment Evolution page for the full list.
