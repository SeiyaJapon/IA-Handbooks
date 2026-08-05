# 06. Anti-patterns

## Monolith on FaaS

A single function handles every operation through internal routing. The team has serverless on paper but a monolith in practice.

Why it fails: cold starts are larger (the bundle has all logic); failures affect everything; observability is per-monolith, not per-operation.

Fix: split per use case or per event type.

## Distributed monolith of functions

Many functions, each calling the next synchronously to complete a workflow. The workflow is a chain of synchronous Lambda calls.

Why it fails: latency stacks; failures cascade; the workflow is hidden across many functions.

Fix: use Step Functions, sagas, or events between functions. Avoid synchronous chains.

## Long-running work in a function

A function that takes minutes for a single invocation. Hits the time limit (15 min for Lambda); fails partway.

Fix: break into smaller pieces (each finishes in seconds). Use Step Functions or queues to coordinate. For genuinely long workloads, use long-running compute.

## Cold start on critical path

A latency-sensitive HTTP endpoint with no cold start mitigation. Some requests get a 3-second cold start; the SLA breaks.

Fix: provisioned concurrency, runtime choice, bundle size optimisation. Or move the endpoint to long-running compute.

## Database connection storm

Every function instance opens its own database connection. Under load, hundreds of instances exhaust the database's connection limit.

Fix: connection pooling at module level + RDS Proxy / PgBouncer. Or use connection-less databases (DynamoDB) for high-throughput functions.

## Module-level cache assumed shared

The team builds an in-memory cache at module level and assumes it works across all function instances. It does not.

Fix: external cache (Redis) for cross-instance state. Module-level cache is per-instance only.

## Vendor blob

The function code is full of platform-specific calls (Lambda context properties, AWS SDK calls, EventBridge schemas). Migrating to another platform requires rewriting most of the code.

Fix: hexagonal core inside the function. The handler knows the platform; the core does not.

## Function per database table

One CRUD function per table. The system is a thin wrapper over the database, with Lambda overhead.

Fix: functions named after business operations, not tables. If the system is genuinely thin CRUD, evaluate whether a managed API service (DynamoDB API, AppSync, Hasura, PostgREST) is a better fit.

## No observability

Functions invoke and fail with no traceability. CloudWatch logs are scattered.

Fix: structured logging, distributed tracing (X-Ray, OpenTelemetry), correlation IDs across functions, dashboards.

## "Lambda is the architecture"

The team adopts Lambda and stops thinking about architecture. Code is whatever fits in the handler.

Fix: serverless is the runtime. The architecture (hexagonal, clean, onion) is a separate decision applied inside the function.

## Output

When reviewing serverless, look for these patterns. Monolith on FaaS, distributed monolith of functions, and database connection storm are the most common.
