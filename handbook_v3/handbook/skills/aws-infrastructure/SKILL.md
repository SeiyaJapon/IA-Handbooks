# AWS Infrastructure

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
