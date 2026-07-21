# Executive View — What Needs Attention

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Executive View](./README.md) · Audience: CTO / Engineering leadership · Confidence: Verified_

## What Needs Attention

The most immediate concern is a cluster of internet-facing security group exposures across both production and development environments — none of which have been confirmed as intentional. No issues currently require an immediate leadership decision, but each exposure warrants engineering validation before it can be closed.

### Highest-Priority Issues

Four verified security exposures are present across the Atlas workload (Confidence: Verified):

| Resource | Environment | Open Port(s) | Impact |
|---|---|---|---|
| cloudox-demo-atlas-prod-alb | Production | — | Internet-facing load balancer |
| cloudox-demo-atlas-prod-sg-edge | Production | 443 | World-open ingress (0.0.0.0/0) |
| cloudox-demo-atlas-prod-sg-alb | Production | 80 | World-open ingress (0.0.0.0/0) |
| cloudox-demo-atlas-dev-sg-ecs | Development | 80 | World-open ingress (0.0.0.0/0) |

The production load balancer (`cloudox-demo-atlas-prod-alb`) is internet-facing, and its associated edge security group permits unrestricted inbound HTTPS (port 443). A second production security group (`cloudox-demo-atlas-prod-sg-alb`) allows unrestricted inbound HTTP (port 80) — unencrypted traffic to a production ALB warrants explicit confirmation. In the development environment, `cloudox-demo-atlas-dev-sg-ecs` similarly allows world-open HTTP ingress.

**Recommended action for all four:** Engineering should confirm each exposure is intentional. Where it is not, ingress rules should be restricted to known CIDR ranges or the load balancer scheme changed to internal.

Additionally, the environment has 75 IAM roles and no customer-managed IAM policies, which limits fine-grained permission control. This is noted as context; detailed IAM analysis is covered in the Security section of this view.

> **Note:** 781 resources carry no Environment/Stage/Tier tag and rely on inference for classification. Tagging gaps may affect the accuracy of environment-level groupings above.

### Changes Since Previous Snapshot

Since the previous snapshot (approximately one hour prior), one new internet exposure was introduced and one was resolved:

- **New exposure:** `cloudox-demo-atlas-dev-sg-ecs` (`sg-0d6a48061beb72eae`) became reachable from the internet — this is the development ECS security group listed in the table above.
- **Resolved exposure:** A previously internet-reachable security group (`sg-04fae132cfc68e91d`) is no longer exposed.

Also observed: Security Hub, SSM, and Step Functions entered the discovered scope; a new DR CloudFormation stack (`StackSet-cloudox-demo-workload-prod-dr`) and associated CloudWatch alarm (`cloudox-demo-atlas-prod-dr-bucket-size`) were added in us-east-1; and a new DynamoDB table (`cloudox-demo-sandbox-events`) appeared in the sandbox account. The 'cloudox-demo-atlas-dev' workload tentatively grew from 4 to 5 member resources, likely reflecting the new ECS security group.

More than 99 additional changes occurred in this snapshot period — see the Environment Evolution page for the full picture.
