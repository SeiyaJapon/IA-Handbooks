# AWS Infrastructure Skill

## When to use

The user asked about AWS infrastructure design (VPC, subnets, ECS clusters, EC2, ALB/NLB, security groups, IAM roles, auto-scaling, multi-AZ).

## When not to use

- Code that uses AWS services (Lambda function code, S3 SDK calls): `aws-review`.
- Provisioning IaC: `terraform-review`.
- Generic cloud strategy decisions across providers: `software-architecture`.
- Compliance posture: `compliance-patterns`.

## Inputs to inspect first

- Network topology: VPCs, subnets (public/private), CIDR ranges, NAT gateways, IGW.
- Routing tables; per-AZ design.
- Security groups and NACLs; inbound/outbound rules; least-privilege scope.
- Load balancer choice: ALB vs NLB; listeners, target groups, health checks.
- ECS / EC2 design: task definitions, capacity providers, service auto-scaling, instance types.
- IAM: instance profiles, task roles, scope.
- Multi-AZ design and failure isolation.
- Cost-relevant resources: NAT gateways, Transit Gateway, premium tiers.

## How to work

1. Inspect topology and IAM scope first.
2. Walk service-by-service concerns; identify single points of failure.
3. Identify cost-impacting resources and ownership boundaries.
4. Group findings by severity.

## Output

Findings grouped: blockers (open security groups to 0.0.0.0/0, single-AZ for critical paths, root-equivalent IAM), defects (oversized instances, oversharing of IAM, weak health checks), nits (tagging, naming).

## Escalation

- Code-level AWS service usage: `aws-review`.
- IaC: `terraform-review`.
- Architectural choice (long-running vs serverless, microservices vs monolith): `software-architecture` and the relevant sub-skill.
- Security posture: `security-review`.
- Compliance: `compliance-patterns`.

---

## Purpose

Review AWS compute and network infrastructure design — EC2, ECS, load balancers, VPC, security groups, and auto-scaling — for correctness, security, and operational soundness.

## Responsibilities

- Review VPC design: subnet segmentation, public/private boundaries, routing tables
- Evaluate security group rules for least privilege
- Assess load balancer selection and configuration (NLB vs ALB, listeners, target groups, health checks)
- Review ECS cluster and service design (task definitions, capacity providers, service auto-scaling)
- Evaluate EC2 instance type selection, placement groups, and lifecycle management
- Assess auto-scaling group configuration (policies, cooldowns, health check grace period)
- Review IAM roles attached to instances and tasks (instance profiles, task roles)
- Evaluate network ACL and security group layering
- Assess multi-AZ design and failure isolation

## Instructions

- Identify the network topology: VPCs, subnets, availability zones, and how traffic flows
- Check security groups: are inbound rules restricted to the minimum required? is `0.0.0.0/0` documented?
- Check load balancer: is NLB or ALB appropriate for the protocol? are health checks correctly tuned?
- Check ECS: are task CPU/memory sized for actual workload? is auto-scaling configured?
- Check EC2: are instances in private subnets? is bastion or SSM used for access (not direct SSH from internet)?
- Check auto-scaling: are cooldown periods appropriate? will scale-in events cause service disruption?
- Check IAM: do instance profiles and task roles follow least privilege?
- Check multi-AZ: are critical services distributed across at least two availability zones?

## Heuristics

Treat as stronger concerns when:

- Security group with inbound `0.0.0.0/0` on port 22 (SSH) or 3306/5432 (database ports)
- EC2 instances in public subnets with direct internet access when not required
- Load balancer health check targeting a path that doesn't reflect actual readiness
- ECS service with no auto-scaling and no minimum healthy percent defined
- Single-AZ deployment for any production-facing service
- NLB used when ALB is needed for HTTP routing, host/path-based rules, or WAF integration
- IAM instance profile with `*` on action or resource
- Auto-scaling scale-in without connection draining or deregistration delay
- No VPC Flow Logs enabled for auditing and incident response

Treat as acceptable when:

- Single-AZ deployment in development or staging environments
- Open security groups in isolated dev VPCs with no production data

## Rules

- Least privilege on all security groups and IAM roles attached to compute resources
- Database ports must never be open to `0.0.0.0/0`
- Production services must be multi-AZ
- SSH from the internet is never acceptable — use SSM Session Manager or bastion in private subnet

## Activity Traceability

🔧 Loading skill: `aws-infrastructure`
