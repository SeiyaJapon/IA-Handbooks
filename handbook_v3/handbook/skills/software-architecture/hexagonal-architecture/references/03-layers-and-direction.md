# 03. Layers and Dependency Direction

Hexagonal expresses itself in code as three layers and one rule about how they depend on each other.

## The three layers

There are exactly three valid layers. No more, no less.

### `domain/`

The core of the core. Contains the business model and nothing else.

What lives here:

- Entities, value objects, aggregates, domain services, domain events, domain errors.
- Pure model: no framework, no ORM, no HTTP, no SDK, no database driver, no logger of a specific library.
- Optionally, ports that are naturally domain-owned (a domain-level repository contract, a domain-level event bus contract).

What does not live here:

- Any import from `application/` or `infrastructure/`.
- Any import from a third-party library that is not strictly in service of expressing the domain (a UUID library is borderline; an ORM is not).
- Any decorator from a framework or library.

The `domain/` layer must be testable on its own, with no infrastructure running.

### `application/`

The orchestration layer. Coordinates the core to satisfy use cases.

What lives here:

- Use cases / application services. One per business operation.
- Driving ports (interfaces that external actors call to reach the core).
- Driven ports (interfaces that the core uses to reach the outside).
- Command, query, and result data carriers.

What does not live here:

- Business rules. They live in `domain/`.
- Concrete adapters. They live in `infrastructure/`.
- Framework code. The application layer is framework-agnostic.

`application/` may import from `domain/`. It must not import from `infrastructure/`.

### `infrastructure/`

Everything that is not the core. All adapters, both driving and driven.

What lives here:

- **Driven adapters**: repositories, external service clients, event bus implementations, file storage, time and identity providers.
- **Driving adapters**: HTTP controllers, CLI handlers, queue consumers, schedulers, message handlers.
- Framework wiring (DI modules, framework configuration).
- Concrete database access, transport libraries, SDKs.

`infrastructure/` may import from `application/` (to satisfy the ports) and from `domain/` (when an adapter needs to construct domain types).

## What does NOT exist as a layer

- `presentation/` is **not** a layer. HTTP controllers, GraphQL resolvers, CLI handlers, queue consumers and any other delivery mechanism are driving adapters and live in `infrastructure/` (typically in subfolders like `infrastructure/http/`, `infrastructure/cli/`, `infrastructure/messaging/`).
- `services/` is not a layer. Services are either domain services (in `domain/`) or application services (in `application/`).
- `controllers/`, `models/`, `views/` are MVC concepts. They are not hexagonal layers.
- `core/` and `shared/` are folders that may exist for organisational reasons but are not part of the hexagonal layer model.

A `presentation/` folder is a finding to fix, not a stylistic choice.

## The dependency rule

Dependencies point **inward only**.

```
infrastructure/  →  application/  →  domain/
```

Concretely:

- `domain/` depends on **nothing** outside itself (with the rare exception of fundamental language types and value libraries that do not couple to a specific framework or platform).
- `application/` depends on `domain/`. Never on `infrastructure/`.
- `infrastructure/` depends on `application/` and `domain/`.

Inversion of this rule, even partial, breaks hexagonal. Examples of inversion:

- `domain/Order.ts` imports `infrastructure/database/Connection.ts`.
- `application/CreateOrderUseCase.ts` imports `infrastructure/postgres/PostgresOrderRepository.ts` directly (instead of depending on the port).
- `domain/PaymentService.ts` uses a decorator from a framework library.

The rule has no exceptions for "convenience". A single inversion is enough to invalidate the architecture for that path.

## How dependencies invert

The dependency rule is preserved through **dependency inversion**.

- The application defines the port (an interface) it needs.
- The infrastructure implements the port.
- At composition time, the concrete adapter is wired into the application.
- The application code never names the adapter; it only names the port.

This is the difference between depending on an abstraction (allowed) and depending on a concrete implementation (forbidden across the inward direction).

## Layer boundaries are enforced, not suggested

The layer boundaries are enforced by:

- **Code review.** A PR that imports across the wrong direction is rejected.
- **Tooling (when available).** Lint rules, dependency graph tools, build configuration that prevents the disallowed imports.
- **Tests.** Domain tests should not require infrastructure to run. If they do, an inversion has happened.

Boundaries that are only documented but not enforced erode under pressure.

## What lives where: placement table

| Concept | Layer | Notes |
|---|---|---|
| Aggregate, entity, value object | `domain/` | Pure model. |
| Domain service | `domain/` | Pure model behaviour. |
| Domain event class | `domain/` | The class definition. |
| Domain error class | `domain/` | Custom errors raised by the domain. |
| Use case / application service | `application/` | Orchestration only. |
| Command, query, result | `application/` | Data carriers. |
| Driving port (interface) | `application/` | Use case interface, command/query handler interface. |
| Driven port (interface) | `application/` (typical) or `domain/` (rare, when domain-owned) | Repository, external client, event bus, time, identity. |
| Driving adapter | `infrastructure/` | HTTP controller, CLI handler, queue consumer. |
| Driven adapter | `infrastructure/` | Repository implementation, external client, event bus implementation. |
| Framework module / DI configuration | `infrastructure/` | Wiring lives outside the core. |
| Database client, ORM setup | `infrastructure/` | Never imported from the core. |

## Empty layers

A layer is created when there is content for it.

- A bounded context that is a thin adapter to a generic subdomain may have **only** `infrastructure/`.
- A context that is pure orchestration over external services may not have a meaningful `domain/`.

Empty `domain/` or empty `application/` folders with only a README are noise. They suggest structure that does not exist. Remove them.

The reverse is also true: a context that has content for all three layers must have all three. Skipping `application/` and putting use cases in `infrastructure/` collapses the boundary.

## Output

For every layer in the codebase, the team can answer:

- Which of the three layers is this?
- What lives here, and what does not?
- What does this layer import, and from which other layer?
- Are the dependency direction rules respected?

If a folder cannot be classified into one of the three layers, it is not part of hexagonal. Either reclassify it or move it out of the architecture's scope.
