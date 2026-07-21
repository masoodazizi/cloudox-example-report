# Generic View — Key Questions Answered

> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

> _Part of the [Generic View](./README.md) · Audience: Any technical reader · Confidence: Unknown_

## Key Questions Answered

### What is this environment and what is it for?

This is a multi-account AWS environment operating under the workspace **cloudox-demo**. It spans **6 of 7 known accounts** across **2 observed regions**, with 1 account excluded from scope. The account structure follows a recognisable landing-zone pattern: a **Management Account** (`110319895932`), a dedicated **Log Archive Account** (`122980216815`), an **Audit Account** (`110019496666`), a **Platform Account** (`150982215529`), a **Workload Dev Account** (`105769365151`), and a **Workload Prod Account** (`122122642149`), plus a **Sandbox Ma Account** (`161388682021`). A total of **833 resources** were captured across these accounts.

Confidence: Likely — account role assignments for Log Archive, Audit, and Platform accounts are inferred rather than directly confirmed.

---

### What are the most significant workloads and systems?

Four significant workloads (of 5 inferred) and 1 system are present. The most clearly evidenced workload is **Cloudox Demo Atlas Prod API** (`cloudox-demo-atlas-prod-api`) in the Workload Prod Account (`122122642149`, eu-central-1), which depends on a PostgreSQL datastore (`cloudox-demo-atlas-prod-pg`) and is fronted by an internet-facing load balancer (`cloudox-demo-atlas-prod-alb`). API Gateway endpoints are observed in eu-central-1 for both dev (`xdmn5ldmif.execute-api.eu-central-1.amazonaws.com`) and prod (`gfwaiva01f.execute-api.eu-central-1.amazonaws.com`). DynamoDB tables are present across dev (`cloudox-demo-atlas-dev-items`), prod (`cloudox-demo-atlas-prod-items`), and sandbox (`cloudox-demo-sandbox-events`, `cloudox-demo-sandbox-scratch`) accounts. The Sandbox environment (`161388682021`) hosts its own isolated workload activity.

Confidence: Likely — workload groupings are inferred from resource naming and account structure.

---

### What is exposed to the internet, and how?

Three security groups are confirmed open to the internet (0.0.0.0/0 or ::/0), and one load balancer is internet-facing:

| Resource | Account | Port | Reference |
|---|---|---|---|
| `cloudox-demo-atlas-prod-alb` (ALB) | Workload Prod (`122122642149`) | — | `security_exposure:security:cloudox-demo-atlas-prod-alb` |
| `cloudox-demo-atlas-prod-sg-edge` | Workload Prod (`122122642149`) | 443 | `sg-0459201826f8de5b3` |
| `cloudox-demo-atlas-prod-sg-alb` | Workload Prod (`122122642149`) | 80 | `sg-06f2b4190bf01d261` |
| `cloudox-demo-atlas-dev-sg-ecs` | Workload Dev (`105769365151`) | 80 | `sg-0d6a48061beb72eae` |

Internet gateways are also observed in eu-central-1 and us-east-1 across multiple accounts. The recommended action for each exposure is to confirm whether the internet-facing configuration is intentional and restrict ingress if not.

Confidence: Verified for the security group and ALB findings.

---

### What are the top risks and recommended actions?

The two highest-priority risks (both medium severity, priority 2) are gaps in detective controls that affect 5 of 6 scanned accounts:

1. **Uneven GuardDuty coverage** — GuardDuty threat detection is enabled in only 1 of 6 accounts; missing from Log Archive (`122980216815`), Workload Dev (`105769365151`), Workload Prod (`122122642149`), Management (`110319895932`), and Sandbox (`161388682021`). *Recommended action: enable org-wide via a delegated administrator.* (`risk:security:aws-guardduty-detector`)

2. **Uneven IAM Access Analyzer coverage** — Access Analyzer is similarly enabled in only 1 of 6 accounts, leaving the same 5 accounts without external-access analysis. *Recommended action: enable org-wide via a delegated administrator.* (`risk:security:aws-accessanalyzer-analyzer`)

Three internet-exposure findings (priority 3) are detailed in the section above. All carry a "confirm and restrict if not required" recommendation.

Confidence: Likely for the GuardDuty and Access Analyzer gaps (based on scanned accounts); Verified for the security group exposures.

---

### What is most likely to affect reliability or cost?

**Reliability:** The production PostgreSQL datastore `cloudox-demo-atlas-prod-pg` is not Multi-AZ. The **Cloudox Demo Atlas Prod API** workload (`cloudox-demo-atlas-prod-api`) depends on it, meaning a single availability-zone failure would take the production data tier offline. Confirmed backup and failover configuration is not evidenced in this section. (`dependency_concern:architecture:cloudox-demo-atlas-prod-pg`)

**Cost:** One NAT Gateway (`nat-05bf82584b9610324`, Workload Dev Account `105769365151`, eu-central-1) carries non-production tags and is a consolidation candidate. NAT Gateways bill per hour and per GB; replacing or removing it in this low-criticality context could reduce spend. Current observed spend is **$80.28** with 8 architectural cost drivers and 1 optimization candidate identified. (`cost_opportunity:cost:nat-gateway-consolidation`)

Confidence: Verified for the single-AZ dependency; Likely for the NAT Gateway cost opportunity (requires validation of egress and HA needs before acting).

---

### What could not be confirmed and should be validated?

Several items warrant direct validation with the environment owner:

- **Account role for Log Archive (`122980216815`)** is inferred (Likely), not directly confirmed. Its environment classification is Assumed.
- **Account IDs for the ALB and PostgreSQL datastore** (`cloudox-demo-atlas-prod-alb`, `cloudox-demo-atlas-prod-pg`) could not be resolved to a specific account in this section's data.
- **GuardDuty and Access Analyzer** — it is possible that org-level delegated administration covers some accounts not reflected in per-account scan results; this should be confirmed before remediation work is scoped.
- **Multi-AZ and backup status** of `cloudox-demo-atlas-prod-pg` is not confirmed; the resilience posture of the production data tier should be verified directly.
- **1 account** was excluded from scope entirely — its contents and risk posture are not represented here.
- The **Audit Account (`110019496666`)** and **Platform Account (`150982215529`)** are present as key entities but their role assignments are inferred (Likely).

Confidence: Unknown for the items listed above — these are validation questions for the environment owner.
