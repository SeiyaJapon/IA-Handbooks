# Serverless Patterns

## Purpose

Review serverless architecture decisions, Lambda design, and event-driven invocation patterns for correctness and operational soundness.

## Responsibilities

- Review Lambda handler design and scope of responsibility
- Assess cold start impact on latency-sensitive functions
- Evaluate concurrency configuration and reserved concurrency
- Review timeout configuration relative to downstream dependencies
- Assess event source mapping and batch size configuration
- Review error handling and retry behavior per invocation model (sync, async, stream)
- Evaluate VPC attachment necessity and cold start impact
- Review Lambda layer usage and dependency packaging

## Instructions

- Identify the invocation model: synchronous (API Gateway), asynchronous (EventBridge, S3), or stream (Kinesis, DynamoDB Streams)
- Check timeout: is it longer than the slowest downstream call?
- Check concurrency: is there a risk of hitting account or function limits?
- Check error handling: for async invocations, is there a DLQ?
- Check cold start sensitivity: is this function in a latency-sensitive synchronous path?
- Check VPC attachment: is it necessary? (adds 100–500ms cold start overhead)
- Check memory sizing: too low causes CPU bottleneck; too high wastes cost
- Check handler: does it do one thing? or is it orchestrating too much?

## Heuristics

Treat as stronger concerns when:

- Timeout shorter than downstream service SLA or response time
- Async Lambda without DLQ
- VPC attachment for a function that only calls external HTTP APIs
- Reserved concurrency set to 0 (blocks all invocations)
- Business logic scattered across many small Lambdas without clear orchestration boundary
- Secrets in environment variables instead of Secrets Manager
- Global state mutation in handlers (state persists across warm invocations)
- Synchronous calls between Lambdas (tight coupling, cascading timeouts)

Treat as acceptable when:

- Simple synchronous Lambda without DLQ — errors surface directly to the caller
- VPC attachment when the function must access RDS or private resources

## Rules

- Always check timeout vs downstream latency
- Async invocations always need a DLQ
- Secrets Manager over environment variables for sensitive configuration
- Global state in Lambda handlers must be treated as potentially warm — never assume cold start

## Activity Traceability

🔧 Loading skill: `serverless-patterns`
