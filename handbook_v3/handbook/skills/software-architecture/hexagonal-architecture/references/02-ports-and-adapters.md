# 02. Ports and Adapters

The two central concepts of hexagonal architecture are **ports** and **adapters**. Everything else (layers, layout, dependency direction) is a consequence of these two.

## Port

A **port** is an interface that the application core defines for an interaction with the outside.

- The port belongs to the core.
- The port is defined in the language of the core, not in the language of the outside.
- The port specifies **what the core needs or offers**, not **how it is implemented**.

There are two kinds of ports.

### Driving ports (input, primary)

A driving port is the interface that **external actors call** to interact with the core. It is the contract that says "the core can do this".

- Belongs to `application/` (or `domain/` when the operation is a pure domain method, which is rare for top-level entry points).
- Typical examples: a use case interface, a command handler interface, a query handler interface.
- The driving adapter (HTTP controller, CLI handler, queue consumer) translates external input into a call on the driving port.

A driving port is named after the operation it offers, in domain terms: `CreateScenarioUseCase`, `EvaluateRiskUseCase`, `ListActiveProductsQuery`.

### Driven ports (output, secondary)

A driven port is the interface that **the core calls** to interact with the outside. It is the contract that says "the core needs this from the outside".

- Belongs to `application/` or `domain/`, depending on whether the need is purely domain or whether application orchestration is involved.
- Typical examples: repository interfaces, external service client interfaces, event bus interfaces, time and identity providers, notification interfaces.
- The driven adapter (Postgres repository, HTTP external client, RabbitMQ event bus, system clock) implements the driven port.

A driven port is named after the capability it abstracts, in domain or application terms: `OrderRepository`, `RiskScoreProvider`, `NotificationDispatcher`.

## Adapter

An **adapter** is the concrete implementation that connects a port to a real technology.

- The adapter belongs to `infrastructure/`.
- The adapter depends on the port (defined in the core) and on the technology it adapts.
- The adapter never leaks the technology back into the core.

There are two kinds of adapters, mirroring the two kinds of ports.

### Driving adapters

A driving adapter accepts input from the outside (HTTP request, CLI invocation, queue message, scheduled trigger) and translates it into a call on a driving port.

- Lives in `infrastructure/`.
- Knows about its transport (HTTP, CLI, queue protocol).
- Knows about the driving port it calls.
- Does not contain business rules.

Examples: `CreateScenarioController` (HTTP), `CreateScenarioCliCommand` (CLI), `ScenarioRequestedConsumer` (queue).

### Driven adapters

A driven adapter implements a driven port using a specific technology.

- Lives in `infrastructure/`.
- Knows about the driven port it implements.
- Knows about the technology (Postgres, MongoDB, an HTTP API, a file system, a clock).
- Translates between the port's domain language and the technology's vocabulary.

Examples: `PostgresOrderRepository`, `HttpRiskScoreProvider`, `RabbitMqEventBus`, `SystemClock`.

## Placement

The placement is non-negotiable.

| Artefact | Where it lives |
|---|---|
| Driving port (interface) | `application/` (typical) or `domain/` (rare) |
| Driven port (interface) | `application/` or `domain/` |
| Driving adapter (implementation) | `infrastructure/` |
| Driven adapter (implementation) | `infrastructure/` |

A port that lives in `infrastructure/` is a violation: it is not a port, it is an implementation surface.

An adapter that lives in `application/` or `domain/` is a violation: it is no longer an adapter, it is the core taking on infrastructure concerns.

## Ports are not implementation surfaces

A port is not an abstract base class with a default implementation. It is not a generic helper. It is not a wrapper around a third-party SDK that exposes the SDK's methods one-to-one.

A port specifies what the core needs in the **core's vocabulary**. If the port mirrors the SDK, the SDK has leaked into the core through the port's signature. The fix is to redesign the port in domain terms and have the adapter do the translation.

## One port, one adapter (at a time)

In production, each port has one active adapter. In tests, each port may have a fake or in-memory adapter. The core does not know which one is active.

Multiple adapters per port (one for production, one for staging, one for tests) are normal. Multiple **active** adapters per port at the same time are unusual; when needed, they are typically composed (a primary, a fallback) and the composition itself is an adapter.

## Ports define dependencies

The core declares the ports it needs as constructor parameters of its application services or use cases. The composition root (typically a framework module file in `infrastructure/`) wires the concrete adapters to the ports.

This means:

- The core never instantiates an adapter directly.
- The core never imports a concrete adapter.
- The core never calls an SDK or a database client.

If any of these happen, the port has been bypassed and the architecture is broken at that point.

## Ports vs ordinary interfaces

Not every interface in the codebase is a port. A port has three properties:

1. **It crosses the boundary between core and outside.** Interfaces between two domain objects, or between two layers of the core, are not ports.
2. **It is owned by the core.** Defined in `domain/` or `application/`, never in `infrastructure/`.
3. **It expresses a need or an offer in the core's vocabulary.** A port that mirrors a database schema is not a port; it is the database schema in disguise.

An ordinary interface inside the core (`PriceCalculator`, `EligibilityRule`) is a domain artefact, not a port. It has no adapter.

## Ports vs commands and queries

In CQRS-style codebases, commands and queries are data carriers; their handlers are driving ports.

- The command class is data.
- The query class is data.
- The result class is data.
- The handler interface (`CreateScenarioCommandHandler`) is the driving port.
- The HTTP controller is a driving adapter that builds the command and calls the handler.

This is consistent with the rest of hexagonal: ports are interfaces, adapters are implementations. CQRS does not change the rule; it only specialises the shape of the input data.

## Anti-patterns

- **Port defined in `infrastructure/`.** The interface lives next to its implementation. The core ends up importing from `infrastructure/`. Move the interface inward.
- **Port that exposes the SDK.** The port has methods like `executeQuery(sql: string)`. The SDK has leaked. Redesign in domain terms.
- **Adapter that contains business rules.** "If the database returns null, decide X". That decision belongs in the core. The adapter only translates.
- **Driving adapter that calls multiple driving ports for one operation.** The driving adapter should call one operation. If it needs to compose, the composition belongs in a use case (a driving port), not in the adapter.
- **Driven adapter that depends on another driven adapter.** Adapters do not depend on each other. If one capability needs another, the application service composes them.
- **Generic `Port<T>` or `Repository<T>` types.** Ports are domain-named. Generic types are a sign that the core is shaped by the persistence model.

## Output

For every interaction between the core and the outside, the team can answer:

- Is this a driving or driven port?
- Where does the port live (which layer)?
- What does the port say in the core's vocabulary?
- Which adapter implements it, and in which technology?
- What happens if the adapter fails?

If any of these questions has no answer, the boundary is improvised.
