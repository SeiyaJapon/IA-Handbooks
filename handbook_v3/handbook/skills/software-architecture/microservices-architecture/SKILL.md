# Microservices Architecture Skill

Use this skill when designing, reviewing, or migrating to/from a microservices architecture. Triggers: microservices, service boundaries, service-per-context, independent deployment, distributed system, service mesh, API gateway.

## What microservices is

Microservices is an architecture where the system is decomposed into **independently deployable services**, each owning its data and communicating with others over the network through explicit contracts.

The defining properties:

- **Independent deployment.** Each service can be deployed without coordinating with others.
- **Data ownership.** Each service owns its data; no shared database.
- **Explicit contracts.** Services communicate through documented APIs or events, not through shared in-process state or direct database access.
- **Network communication.** Services talk over HTTP, gRPC, or messaging; not in-process.

## What microservices is and is not

Microservices is an **architecture and deployment pattern**. It is not:

- A synonym of "many services". A system with many services and a shared database is a distributed monolith, not microservices.
- A synonym of "containers" or "Kubernetes". Those are operational platforms.
- A synonym of "small services". Size is not the property; independence is.
- An automatic consequence of DDD. Bounded contexts can be implemented as modules in a monolith.
- A solution to bad architecture. Microservices on top of bad architecture produces a distributed disaster.

## Non-negotiable rule

Never start microservices work from "let's split into services".

The mandatory order is:

1. Verify the forces (multiple teams, asymmetric change rates, independent release cadence, scale).
2. Identify bounded contexts (this is a DDD/architecture decision, not a deployment one).
3. Modularise inside the monolith first if not already done.
4. Stabilise contracts between modules (events, well-defined interfaces).
5. Pick the first extraction (a context with few inbound dependencies, mid complexity, clear ownership).
6. Extract: move to its own service, replace in-process calls with network calls.
7. Operate the new service before extracting the next.
8. Stop when the forces are satisfied. Not every module must become a service.

## Hard rules

### Each service owns its data

No shared database. No service reads or writes another service's tables. If two services need shared data, the owner exposes an API or publishes events; the other service consumes through that contract.

### Independent deployment

A service must be deployable without coordinating with another. If deploying service A requires deploying service B at the same time, they are coupled at release time and the boundary is fictional.

### Explicit contracts, versioned

Inter-service contracts are documented and versioned. Breaking changes require migration. Producers and consumers can evolve independently within the contract.

### Failure isolation

A service must handle the case where another service is slow, down, or wrong. Timeouts, circuit breakers, retries with backoff, dead-letter queues. A synchronous chain of N services has the failure probability of all N services combined.

## Forbidden shortcuts

- "We split, but kept the database shared for now."
- "We have microservices because we deploy with Kubernetes."
- "Each service is a NestJS app, that is microservices."
- "DDD says bounded contexts, so we made one microservice per context."
- "We extracted the service first, will modularise later."

## Mandatory review behavior

When reviewing microservices, check in order:

1. Are the forces actually satisfied (multiple teams, asymmetric change, independent release)?
2. Does each service own its data, with no shared databases?
3. Can each service be deployed independently?
4. Are inter-service contracts explicit, documented, versioned?
5. Are there synchronous chains across three or more services (cascading failure risk)?
6. Are consumers idempotent if delivery is at-least-once?
7. Are operational concerns covered (observability, distributed tracing, circuit breakers, dead-letter queues)?
8. Internally, is each service well-architected (typically hexagonal/clean/onion)?

## When to pick microservices

- Multiple teams need independent release cadence.
- Asymmetric change rates: parts of the system change at very different speeds.
- Scale demands separate deployment units (different scaling profiles, different SLAs).
- Operational maturity exists (deployment automation, observability, on-call).

## When NOT to pick microservices

- Single team, single context. The cost is not justified.
- Operational maturity not yet built. Microservices without monitoring, tracing, and on-call is a recipe for outages.
- The team is migrating "to be modern". Without forces, the cost outweighs the benefit.
- Shared database is unavoidable in the short term.

## References

- `references/01-foundations.md` for what microservices is, its origin, when it applies.
- `references/02-service-boundaries.md` for how to draw service boundaries (typically per bounded context).
- `references/03-data-ownership.md` for the rule that each service owns its data.
- `references/04-inter-service-communication.md` for sync vs async, choreography vs orchestration, contracts.
- `references/05-failure-modes.md` for cascading failures, idempotency, circuit breakers, retries.
- `references/06-relationship-with-other-disciplines.md` for microservices vs monolith, vs hexagonal/clean/onion (composable internally), vs DDD (composable), vs EDA (composable), vs serverless (composable).
- `references/07-anti-patterns.md` for distributed monolith, shared database, synchronous chains.
- `references/08-review-checklist.md` for reviewing a microservices system.
- `references/09-user-preferences.md` for Francisco's project conventions.
