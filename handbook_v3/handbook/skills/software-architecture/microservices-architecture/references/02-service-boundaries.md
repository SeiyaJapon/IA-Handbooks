# 02. Service Boundaries

The single most consequential decision in microservices: where to draw the lines between services.

## The default rule: one service per bounded context

A bounded context (DDD, see `software-design/ddd/`) is a region of the model with one ubiquitous language and one set of rules. It is the natural seam for a service boundary.

- One bounded context per service.
- The service owns the model of that context.
- Cross-context communication uses events or published interfaces, never direct database access.

This is the default because bounded contexts are designed for autonomy: separate language, separate rules, separate change rate. Those are exactly the properties microservices needs.

## When the default does not fit

Sometimes a service is **smaller** than a bounded context:

- A high-throughput operation extracted as a service for scaling.
- A regulatory-isolated capability extracted to satisfy compliance.

Sometimes a service is **larger** than a bounded context:

- Two contexts that always change together can share a service in the early stages, with the boundary in mind for future extraction.

These deviations should be deliberate, documented, and time-bounded. The default is one bounded context per service.

## What is a good boundary

A good service boundary is:

- **Coherent in business terms.** A team can describe what the service is for in one sentence.
- **Stable over time.** The boundary does not move every release.
- **Loosely coupled to other services.** Few inbound and outbound dependencies.
- **High in cohesion internally.** What is inside the service belongs together.
- **Owned by one team.** Conway's law in reverse: align team and service.

A bad service boundary is:

- "Here is where the database is split, so here is the service".
- "Each table is a service".
- "Each entity is a service".
- "Whoever shouts loudest gets a service".

## Modular monolith first

When the boundaries are not yet stable, do not extract. Build a **modular monolith**: bounded contexts as modules with explicit contracts, but in one deployment unit.

Once the contracts are stable and the forces justify extraction, the modular monolith becomes a microservice system by extracting modules one by one. The contracts are already there; only the deployment changes.

Skipping the modular monolith and extracting directly is the most common cause of distributed monoliths.

## Service size

Microservices does not prescribe a size. "Micro" refers to scope, not lines of code. A service can be:

- A few hundred lines (a small focused capability).
- Tens of thousands of lines (a complex bounded context with rich behaviour).

The rule: small enough to be owned by one team, large enough to be coherent.

## Anti-patterns in boundary design

- **Database-shaped services.** The service boundary follows the database schema. Result: services that map one-to-one with tables.
- **Verb-shaped services.** Services named after operations ("OrderProcessing", "PaymentValidation") instead of business capabilities. Result: many services that all do part of one workflow, with chains of synchronous calls.
- **Frontend-shaped services.** Services that mirror UI screens. Result: every UI change requires changes in multiple backend services.
- **CRUD per entity.** One service per entity, each implementing CRUD. Result: distributed monolith with extra HTTP hops.
- **Boundaries imposed by the platform.** "Each Lambda is a service". Lambdas are deployment units; service boundaries are conceptual.

## Output

For each service in the system, the team can answer:

- Which bounded context does it own?
- What is the boundary (in business terms, in one sentence)?
- Why was this boundary chosen?
- Which team owns it?

Without these, the boundaries are accidental and unstable.
