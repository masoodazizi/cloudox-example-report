# Architect View — Key Questions Answered

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Architect View](./README.md) · Audience: Solutions / Cloud Architects · Confidence: Unknown_

## Key Questions Answered

### What is the overall architectural shape of this environment?

The environment spans **6 of 7 known accounts** across **2 observed regions**, with 833 resources scanned. The account structure follows a recognisable landing-zone pattern: a **Management Account** (110319895932), a **Log Archive Account** (122980216815), two workload accounts (**Workload Dev Account** 105769365151 and **Workload Prod Account** 122122642149), and a **Sandbox Ma Account** (161388682021). The primary workload region is **eu-central-1**, with at least one internet gateway observed in **us-east-1** as well. Networking infrastructure includes 15 VPCs, 63 subnets, and 1 load balancer. Internet connectivity is present via 9 observed internet-facing access paths, including API Gateway endpoints and internet gateways across multiple accounts.

Confidence: Likely — account structure and resource counts are derived from scanned data; 1 account was excluded from scope.

### Which workloads and systems matter most, and how are they composed?

Four significant workloads are inferred across the environment, with one system grouping:

- **Cloudox Demo Atlas Prod API** (`cloudox-demo-atlas-prod-api`) — in the Workload Prod Account (122122642149), eu-central-1. Fronted by the internet-facing ALB `cloudox-demo-atlas-prod-alb` and backed by a DynamoDB table (`arn:aws:dynamodb:eu-central-1:122122642149:table/cloudox-demo-atlas-prod-items`). This is the highest-visibility production workload.
- **Cloudox** (`cloudox`) — Verified workload in the Workload Prod Account (122122642149), eu-central-1.
- **Cloudox Demo Atlas Dev** (`cloudox-demo-atlas-dev`) and **Cloudox Demo Atlas Dev API** (`cloudox-demo-atlas-dev-api`) — in the Workload Dev Account (105769365151), eu-central-1. The dev API is reachable via an API Gateway endpoint (`https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com`) and backed by a DynamoDB table (`arn:aws:dynamodb:eu-central-1:105769365151:table/cloudox-demo-atlas-dev-items`).
- **Cloudox Demo Sandbox Scratch** (`cloudox-demo-sandbox-scratch`) — in the Sandbox Ma Account (161388682021), with two DynamoDB tables (`cloudox-demo-sandbox-events`, `cloudox-demo-sandbox-scratch`).

One system grouping, **Cloudox Demo Atlas Dev**, aggregates the dev-side workloads (Assumed confidence — inferred grouping).

Confidence: Likely for prod/dev workloads; Assumed for sandbox workload and system grouping.

### What are the most important dependencies and coupling risks?

The most concrete dependencies visible in this section are:

- **API Gateway → backend services**: Two API Gateway endpoints are observed in eu-central-1 — `https://gfwaiva01f.execute-api.eu-central-1.amazonaws.com` (dev) and `https://xdmn5ldmif.execute-api.eu-central-1.amazonaws.com` — representing internet-reachable entry points whose downstream targets are not fully characterised in this section.
- **ALB → workload**: `cloudox-demo-atlas-prod-alb` is internet-facing and sits in the Workload Prod Account, creating a direct internet-to-workload dependency path.
- **DynamoDB as a data tier**: Both prod (`cloudox-demo-atlas-prod-items`) and dev (`cloudox-demo-atlas-dev-items`) workloads depend on DynamoDB tables in their respective accounts. The sandbox account holds two additional tables (`cloudox-demo-sandbox-events`, `cloudox-demo-sandbox-scratch`). Cross-account data coupling is not evidenced in this section.
- **NAT Gateway**: A NAT Gateway (`nat-05bf82584b9610324`, Cloudox Demo Atlas Dev NAT) exists in the Workload Dev Account (105769365151, eu-central-1), indicating private-subnet workloads with outbound internet dependency.

Coupling risk: The absence of customer-managed IAM policies (0 observed) alongside 75 IAM roles raises a question about how fine-grained access control is enforced between these components — this is a gap rather than a confirmed risk.

Confidence: Likely for the dependency paths; Unknown for cross-account coupling depth.

### How is the network connected and segmented?

The environment has **15 VPCs** and **63 subnets** across the scanned accounts. Public subnets with direct internet gateway routes are confirmed in both the Workload Dev Account and Workload Prod Account in eu-central-1:

- `subnet-0f64d71c952a7898a` (cloudox-demo-atlas-dev-public-b, 105769365151, eu-central-1) — routes to an internet gateway.
- `subnet-016c22941a019a137` (cloudox-demo-atlas-prod-public-b, 122122642149, eu-central-1) — routes to an internet gateway.

Additional public subnets are present in both workload accounts and the sandbox account (eu-central-1). Internet gateways are observed in eu-central-1 across accounts 110019496666 and 105769365151, and in us-east-1 for account 110019496666. The presence of a NAT Gateway in the dev account confirms a private-subnet tier exists there. Segmentation depth (e.g., NACLs, VPC peering, Transit Gateway) is not characterised in this section.

Nine internet-facing access paths are observed in total, spanning API Gateway endpoints, internet gateways, and the internet-facing ALB.

Confidence: Verified for the specific subnets and gateways cited; broader segmentation posture is not fully covered here.

### What design risks and modernization opportunities exist?

**Design risks (medium severity, all confirmed or likely):**

1. **Uneven GuardDuty coverage** (`risk:security:aws-guardduty-detector`): GuardDuty is enabled in only 1 of 6 scanned accounts. The five uncovered accounts include both workload accounts, the management account, the log archive account, and the sandbox — leaving the majority of the estate with reduced threat detection. Recommended: enable org-wide via a delegated administrator.

2. **Uneven IAM Access Analyzer coverage** (`risk:security:aws-accessanalyzer-analyzer`): Access Analyzer is similarly enabled in only 1 of 6 accounts, with the same five accounts uncovered. This reduces visibility into unintended external resource access. Recommended: enable org-wide via a delegated administrator.

3. **Overly permissive security groups**:
   - `cloudox-demo-atlas-prod-sg-edge` (`sg-0459201826f8de5b3`, Workload Prod, eu-central-1): world-open ingress on port 443.
   - `cloudox-demo-atlas-prod-sg-alb` (`sg-06f2b4190bf01d261`, Workload Prod, eu-central-1): world-open ingress on port 80.
   - `cloudox-demo-atlas-dev-sg-ecs` (`sg-0d6a48061beb72eae`, Workload Dev, eu-central-1): world-open ingress on port 80.
   Port 443 open to the world on an edge/ALB group may be intentional for a public API; port 80 open on both the ALB group and the ECS group warrants review — HTTP without redirect or restriction is a common misconfiguration.

4. **Internet-facing ALB** (`security_exposure:security:cloudox-demo-atlas-prod-alb`): `cloudox-demo-atlas-prod-alb` is scheme `internet-facing`. Confirm this is required for the prod API use case; if internal consumers only, switching to internal scheme reduces attack surface.

**Modernization opportunities:** The package surfaces 1 optimization candidate and 8 architectural cost drivers (details are covered in the cost and optimization sections of this view). No specific modernization targets are characterised within this section's scope beyond the security control gaps above.

Confidence: Verified for the security group and subnet exposures; Likely for the GuardDuty and Access Analyzer gaps.

### What architectural assumptions still need validation?

The following gaps and assumptions are material for architects:

1. **Log Archive Account GuardDuty/Access Analyzer status** (`122980216815`): Marked as Likely (not Verified) in both risk items — the account's actual enablement state should be confirmed directly.
2. **Port 80 on prod ALB and ECS security groups**: It is not known from this section whether HTTP is intentionally exposed (e.g., for redirect to HTTPS) or is an oversight. Validation with the workload team is needed.
3. **Cross-account networking**: With 15 VPCs across 6 accounts, the presence or absence of Transit Gateway, VPC peering, or PrivateLink connections is not characterised in this section — covered elsewhere in the view.
4. **IAM access control granularity**: Zero customer-managed IAM policies are observed alongside 75 roles. Whether access control relies entirely on AWS-managed policies or inline policies is not confirmed here.
5. **us-east-1 internet gateway** (`arn:aws:ec2:us-east-1:110019496666:internet-gateway/igw-00ed21b9a0e6596a8`, `arn:aws:ec2:us-east-1:105769365151:internet-gateway/igw-0567575921f471548`): Workloads or resources in us-east-1 are not characterised in this section — their purpose and ownership should be confirmed.
6. **Sandbox workload grouping**: `cloudox-demo-sandbox-scratch` is Assumed confidence — the actual composition and owner of sandbox resources needs validation.

Confidence: Unknown — these are open questions for the environment owner.
