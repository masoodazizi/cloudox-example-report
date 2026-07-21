> **Demo Report** — AWS account IDs and resource identifiers in this report have been
> replaced with synthetic equivalents for public safety. Architecture, workloads,
> findings, and relationships are based on a real AWS environment.

---

[Reference](./README.md)

# Service Dependency Reference

Dependencies between services/resources that are evidenced by an explicit graph edge (each row carries its relationship evidence). Absence of a row means no dependency was discovered, not that none exists.

_Displaying all **12** row(s) (no rows omitted)._

_Sorted by source resource, then dependency type (stable, not by risk)._

| Source Resource | Source Type | Depends On | Dependency Type | Relationship Evidence | Confidence | Evidence |
|---|---|---|---|---|---|---|
| 3869aa71501448f98b6ced83e40d4235 | AWS::ECS::Task | cloudox-demo-atlas-dev-api:1 | depends on | ECS task uses task definition. | Verified | `3869aa71501448f98b6ced83e40d4235`, `cloudox-demo-atlas-dev-api:1` |
| 7aa166e18de34efd8699a663e13265f1 | AWS::ECS::Task | cloudox-demo-atlas-prod-api:2 | depends on | ECS task uses task definition. | Verified | `7aa166e18de34efd8699a663e13265f1`, `cloudox-demo-atlas-prod-api:2` |
| 9a0f16be20cc421b961a1b101524e084 | AWS::ECS::Task | cloudox-demo-atlas-prod-api:2 | depends on | ECS task uses task definition. | Verified | `9a0f16be20cc421b961a1b101524e084`, `cloudox-demo-atlas-prod-api:2` |
| 9c2924f01ffd402eaafca2bdf2d04d67 | AWS::ECS::Task | cloudox-demo-atlas-dev-api:1 | depends on | ECS task uses task definition. | Verified | `9c2924f01ffd402eaafca2bdf2d04d67`, `cloudox-demo-atlas-dev-api:1` |
| cloudox-demo-atlas-dev-api | AWS::ECS::Service | cloudox-demo-atlas-dev-api:1 | depends on | ECS service uses task definition. | Verified | `cloudox-demo-atlas-dev-api`, `cloudox-demo-atlas-dev-api:1` |
| cloudox-demo-atlas-dev-api (gfwaiva01f) | AWS::ApiGatewayV2::Api | cloudox-demo-atlas-dev-api | routes to | HTTP API integration forwards requests to Lambda function. | Verified | `gfwaiva01f`, `cloudox-demo-atlas-dev-api` |
| cloudox-demo-atlas-prod-api | AWS::ECS::Service | cloudox-demo-atlas-prod-api:2 | depends on | ECS service uses task definition. | Verified | `cloudox-demo-atlas-prod-api`, `cloudox-demo-atlas-prod-api:2` |
| cloudox-demo-atlas-prod-api | AWS::ECS::Service | cloudox-demo-atlas-prod-tg | routes to | ECS service registers tasks in target group. | Verified | `cloudox-demo-atlas-prod-api`, `cloudox-demo-atlas-prod-tg` |
| cloudox-demo-atlas-prod-api (xdmn5ldmif) | AWS::ApiGatewayV2::Api | cloudox-demo-atlas-prod-api | routes to | HTTP API integration forwards requests to Lambda function. | Verified | `xdmn5ldmif`, `cloudox-demo-atlas-prod-api` |
| cloudox-demo-atlas-prod-pg | AWS::RDS::DBInstance | stackset-cloudox-demo-workload-prod-3fa47485-72dc-52dd-4672-e5243b802465-rdsparametergroup-jegbgv8kifvi | depends on | RDS instance uses customer-managed DB parameter group. | Verified | `cloudox-demo-atlas-prod-pg`, `stackset-cloudox-demo-workload-prod-3fa47485-72dc-52dd-4672-e5243b802465-rdsparametergroup-jegbgv8kifvi` |
| cloudox-demo-atlas-prod-tg | AWS::ElasticLoadBalancingV2::TargetGroup | cloudox-demo-atlas-prod-alb | member of | Target group is attached to load balancer. | Verified | `cloudox-demo-atlas-prod-tg`, `cloudox-demo-atlas-prod-alb` |
| 9b700d6b85e65ced | AWS::ElasticLoadBalancingV2::Listener | cloudox-demo-atlas-prod-tg | routes to | Listener default action routes to target group. | Verified | `9b700d6b85e65ced`, `cloudox-demo-atlas-prod-tg` |

**Coverage notes**

- Only relationships materialised in the knowledge graph are listed; dependencies via application config, code, or uncollected services are not represented.
