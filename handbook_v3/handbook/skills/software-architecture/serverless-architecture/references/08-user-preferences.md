# 08. User Preferences (Francisco)

Project conventions for serverless architecture in Francisco Pérez's projects at Kintai.

## When serverless is acceptable

Serverless is acceptable when:

- The workload is bursty, event-driven, or has low constant load.
- Cold starts are acceptable for the path or are mitigated (provisioned concurrency).
- The workload completes within the platform's time limit.
- Vendor coupling is contained to the handler (hexagonal core inside).

## When serverless is rejected

- Latency-critical synchronous paths without cold start mitigation budget.
- Long-running processes (sustained background work, long batch jobs that exceed the platform limit).
- Stateful workloads that need in-memory persistence.

## Architecture inside the function

Each serverless function is hexagonal internally:

- Handler is the driving adapter.
- Application core is portable.
- Persistence and external clients are driven adapters.

The function bundle imports a shared core (a TS package) when many functions share logic.

## Default platform

AWS Lambda is the default platform for the Kintai backend's serverless functions. Vendor coupling lives in the handler; the core is portable.

## Function granularity

- One function per HTTP endpoint (HTTP-triggered functions).
- One function per event type (event-triggered functions).
- One function per scheduled job.

Mega-handlers that route internally are forbidden.

## Cold start strategy

- Latency-critical synchronous paths use provisioned concurrency or are migrated to long-running compute.
- Async paths accept cold starts.
- Bundle size is monitored. Trees are shaken. Heavy libraries are avoided.

## State and connections

- Database connections pool at module level.
- Critical Lambda → RDS paths use RDS Proxy.
- High-throughput paths use connection-less databases (DynamoDB) where the workload fits.
- Secrets come from AWS Secrets Manager / Parameter Store, fetched at module level and cached for the instance lifetime.

## Workflows

- Multi-step async workflows use Step Functions or sagas (events between Lambdas).
- Synchronous chains of three or more Lambdas are forbidden.

## Observability

- Every function instruments distributed tracing (X-Ray or OpenTelemetry).
- Logs are structured.
- Correlation IDs flow across function calls.
- CloudWatch dashboards and alerts are configured before production.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the term `consumer` (used elsewhere in this project for SQS workers) applies to Lambdas triggered by SQS, or whether `lambda` is the canonical term.
- Whether the project standardises on a serverless framework (Serverless Framework, AWS SAM, AWS CDK) or per-repo choice.
- Whether the function-per-use-case granularity is applied uniformly, or if some teams group multiple use cases per function for cost reasons.

These are open. Do not assume; ask.
