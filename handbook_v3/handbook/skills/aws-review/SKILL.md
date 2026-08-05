# AWS Review Skill

## When to use

The user asked for a review of code that uses AWS services (Lambda, EventBridge, S3, SQS, SNS, DynamoDB, IAM, API Gateway). Focus on correctness, idioms, IAM scope, and operational pitfalls.

## When not to use

- Cloud architecture design (VPC, ECS, ALB/NLB, multi-AZ, network topology): `aws-infrastructure`.
- Provisioning IaC: `terraform-review`.
- Container builds: `docker-review`.
- Generic security review: `security-review` (this skill checks AWS idioms; security-review covers wider posture).

## Inputs to inspect first

- Which AWS services are in use; SDK version (v2 vs v3); language.
- IAM policies attached to Lambdas / roles. Scope, conditions.
- Event source mapping: triggers, batch size, max concurrency, DLQ.
- S3 bucket policies, encryption, public access settings.
- Lambda runtime, memory, timeout, concurrency settings, layers.
- EventBridge: rules, targets, retry policy, archive.
- DynamoDB: tables, indexes, capacity model, partition key choice.
- API Gateway: type (REST, HTTP), authorisers, throttling.

## How to work

1. Inspect service usage and IAM scope first.
2. Walk per-service concerns (Lambda cold start and connections, EventBridge retries, SQS visibility timeout, SNS fanout, S3 encryption and versioning, DynamoDB partition design).
3. Group findings by severity. Do not modify infrastructure.

## Output

Findings cited by service and resource, grouped: blockers (overly permissive IAM, public S3, missing DLQ, no encryption), defects (cold-start exposure, idempotency gaps, schema drift on EventBridge), nits (tagging, naming).

## Escalation

- Architecture decisions (which service to use, multi-AZ, scale shape): `aws-infrastructure`.
- IaC: `terraform-review`.
- Security posture beyond IAM: `security-review`.
- Compliance and regulated data: `compliance-patterns`.
- Event-driven design: `event-driven-architecture`.
- Serverless architecture: `serverless-architecture`.
- Long-running services: `long-running-services-architecture`.

---

## Purpose

Review AWS architecture decisions, service selection, and configuration for correctness, security, cost efficiency, and operational soundness.

## Responsibilities

- Review Lambda function design (handler, concurrency, timeout, memory)
- Evaluate EventBridge rules, patterns, and targets
- Assess S3 bucket configuration (lifecycle, access control, versioning)
- Review IAM roles and policies for least privilege
- Evaluate SQS/SNS topology and dead-letter queue configuration
- Assess API Gateway setup (auth, throttling, CORS)
- Review CloudWatch alarms and log retention
- Detect cross-service coupling and failure propagation
- Assess cost implications of service choices and configurations

## Instructions

- Identify which AWS services are involved and how they interact
- Check IAM for overly broad permissions — every `*` is a signal
- Check Lambda: timeout vs downstream latency, memory sizing, concurrency limits
- Check EventBridge: pattern coverage, retry policy, DLQ
- Check SQS: visibility timeout vs processing time, DLQ configuration
- Check S3: explicit ACL or bucket policy, public access block, versioning
- Check secrets: are they in Secrets Manager or in environment variables?
- Ask: what happens when each service fails?

## Heuristics

Treat as stronger concerns when:

- IAM with `*` on resource or action without documented justification
- Lambda timeout shorter than downstream service SLA
- Async invocations without DLQ
- S3 buckets without explicit access control
- Secrets or API keys in Lambda environment variables
- Missing CloudWatch alarms on critical Lambda paths
- EventBridge without retry policy or DLQ
- Cross-account or cross-region access without explicit justification

Treat as acceptable when:

- Broad IAM in dev/sandbox environments with documented rationale
- Simple synchronous Lambda without DLQ (errors surface to the caller)
- Missing alarms on non-critical background jobs in early development

## Rules

- Least privilege on every IAM change — no exceptions
- Always ask: what happens on failure?
- Do not recommend new services without considering cost and operational overhead
- Flag any public-facing resource without explicit justification

## Activity Traceability

🔧 Loading skill: `aws-review`
