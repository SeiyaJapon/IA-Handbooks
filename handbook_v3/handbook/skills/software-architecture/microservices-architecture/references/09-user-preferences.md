# 09. User Preferences (Francisco)

Project conventions for microservices in Francisco Pérez's projects at Kintai.

## When microservices is acceptable

Microservices is acceptable when **all** of the following are true:

- Multiple teams need independent release cadence.
- Bounded contexts are clearly identified (DDD work is done).
- Operational maturity exists: automated deploys, observability, tracing, on-call.
- A modular monolith has been considered and rejected with a documented reason.

## Default: modular monolith first

Default for backend services in this project is **hexagonal in a modular monolith** (one repo, contexts as folders, no cross-context imports). Microservices is a deliberate next step, not the starting point.

## Service boundaries

- One bounded context per service is the default.
- Service names match the bounded context name (`auth-service`, `payment-service`, `product-service`).
- A service is owned by one team. The owner is documented.

## Data ownership

- Each service has its own database (or its own schema if databases are shared at the platform level).
- No shared tables across services.
- Cross-service reads go through the owner's API or via events.

## Communication

- **Async (events) is the default** for inter-service communication.
- Synchronous calls are allowed when the caller genuinely needs the response immediately, with timeouts, circuit breakers, and retries with backoff.
- Synchronous chains of three or more services are forbidden without an explicit justification.

## Contracts

- Every inter-service interaction is on a documented contract.
- API contracts use OpenAPI.
- Event contracts use a documented schema (JSON Schema or Avro depending on the broker).
- Breaking changes require versioning and a migration plan.

## Operational requirements

For a service to ship to production:

- Distributed tracing instrumented.
- Structured logs to the central log system.
- Metrics published.
- Health endpoints implemented.
- Alerts configured.
- Dead-letter queues configured for messaging.
- Transactional outbox where event publication and state change must be atomic.

## Internal architecture

Each microservice is hexagonal internally by default (Francisco's project default). See `software-architecture/hexagonal-architecture/`.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the canonical communication style is event-only, or whether the project standardises a sync transport (HTTP, gRPC) for the rare sync cases.
- Whether sagas have a project-wide framework (a saga library) or each service rolls its own.
- Whether each service must include a published SDK (TS package) or whether consumers parse the API directly.
- Whether deployment is per-service or platform-managed (one shared cluster).

These are open. Do not assume; ask.
