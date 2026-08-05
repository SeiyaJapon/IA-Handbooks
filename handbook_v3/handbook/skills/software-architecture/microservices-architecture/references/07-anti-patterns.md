# 07. Anti-patterns

## Distributed monolith

Many services, but they cannot be deployed independently. Shared database, synchronous chains, releases coordinated across services.

Why it fails: all the operational cost of microservices, none of the benefit.

How to recognise:

- Releases require coordinating multiple services.
- Schema changes require multi-service deploys.
- One service down brings others down.

Fix: identify the coupling (shared database, synchronous chains, undocumented contracts) and break it. If breaking is not feasible, consolidate back into a modular monolith.

## Shared database

Two or more services connect to the same database.

Fix: split the database. Each service owns its data.

## Synchronous chain across services

A → B → C → D synchronously. Latencies stack; failures cascade.

Fix: break the chain. Use events between services (each service handles one stage and publishes), or accept the latency and add resilience (timeouts, circuit breakers, fallbacks).

## Service per entity

One service per database table or per entity. Hundreds of tiny services that all do CRUD.

Why it fails: the system is a distributed CRUD. Every operation requires multiple service calls. No service has meaningful business logic.

Fix: collapse. Services should be one bounded context, not one entity.

## Verb-shaped services

Services named after operations ("PaymentValidationService", "OrderProcessingService"). Each step of a workflow is its own service.

Why it fails: workflows become synchronous chains of services. No service owns a coherent capability.

Fix: services are named after business capabilities (nouns, in DDD terms), not operations.

## Microservices because cargo cult

The team adopted microservices because Netflix did. Forces are not aligned.

Fix: revisit the forces. If microservices is not justified, consolidate into a modular monolith.

## At-most-once messaging

Events are lost. The system tolerates loss "because retry exists".

Fix: at-least-once delivery + idempotent consumers. Use a transactional outbox to avoid losing events between commit and publish.

## No idempotency

Consumers process duplicates as if they were new. Effects multiply.

Fix: idempotent handlers, dedup tables.

## No observability

A failed request cannot be traced across services. Logs are per-service. Metrics are not aggregated.

Fix: distributed tracing (OpenTelemetry, X-Ray), structured logs, aggregated metrics, dashboards per service and end-to-end.

## No contracts

Services exchange undocumented JSON. Contract changes break consumers.

Fix: documented and versioned contracts (OpenAPI, AsyncAPI, Protobuf, JSON Schema). Consumer-driven contract testing where applicable.

## Deployment coupling

Service A and B must always be deployed together because their contract is implicit.

Fix: explicit, versioned contracts. Backward-compatible changes. Migration plans for breaking changes.

## Operational immaturity

Microservices adopted before the team can run them: no automated deploys, no monitoring, no on-call. Outages everywhere.

Fix: stop adding services. Build operational maturity. Consolidate if necessary.

## Output

When reviewing microservices, look for these patterns by name. Distributed monolith and shared database are the most common; they invalidate the architecture.
