# 07. Application Layer

In DDD, the application layer is the **orchestration layer** that coordinates the domain model to satisfy use cases. It contains use cases (also called application services), and the data carriers (commands, queries, results) that those use cases accept and return.

The application layer is thin. It has no business decisions. Every decision belongs in the domain. The application layer's job is coordination.

This document covers the application layer as a DDD concept. Where the application layer lives in the codebase, what surrounds it (controllers, repositories, frameworks), and how it connects to the outside are **architectural decisions** governed by the architecture skill in use (hexagonal, clean, onion, etc.). Do not look here for those answers.

## What lives in the application layer (DDD perspective)

- **Use cases / application services.** One per business operation. They orchestrate; they do not decide.
- **Command handlers and query handlers.** When CQRS-style separation is used, commands and queries have separate handlers. When it is not, the same use case handles both reads and writes.
- **Command, query, and result types.** Data carriers between the outside and the use case. They are not domain entities; they are application-level data carriers.
- **Contracts the application needs from the outside.** Repository contracts, event bus contracts, identity generators, time providers, external service contracts, notifications. The exact shape and placement of those contracts is an architectural concern; in DDD they are simply named "what the application needs".

What does not live in the application layer:

- Business rules. They live in the domain.
- Framework code. The application layer is framework-agnostic.
- Infrastructure mechanics (HTTP serialisation, SQL, queue protocols). Those are architectural concerns.

## Use cases

A use case is the unit of orchestration for a single business operation: "create a scenario", "approve a payment", "list active products".

Properties:

- **One use case per operation.** A use case named `OrderUseCase` with twelve methods is twelve use cases hidden in one class.
- **Named after the operation.** `CreateScenarioUseCase`, `ApprovePaymentUseCase`, `ListActiveProductsUseCase`.
- **Thin.** The body reads as a sequence of orchestration steps: load aggregate, call domain method, save, dispatch events, return result.
- **Stateless.** A use case does not hold per-request state. It is invoked, it runs, it returns.

A typical command use case body:

```
1. Validate command shape (structural validation only; not business rules).
2. Load aggregate(s) from the repository contract.
3. Call a method on the aggregate. The aggregate enforces invariants and records domain events.
4. Save the aggregate through the repository contract (within a transaction).
5. Pull recorded events from the aggregate.
6. Dispatch events through the event bus contract.
7. Return result.
```

A typical query use case body:

```
1. Validate query shape.
2. Read from repository or read-model contract.
3. Map to result type.
4. Return result.
```

If a use case body is longer than this and does not just decompose into smaller helpers, the use case is doing more than one thing.

## Commands, queries, and results

Data carriers between the outside and use cases:

- **Command.** A request to change state. Named in imperative: `CreateScenarioCommand`, `ApprovePaymentCommand`. Contains the data needed to perform the operation.
- **Query.** A request for data. Named for the data wanted: `GetScenarioByIdQuery`, `ListActiveProductsQuery`.
- **Result.** The output of a query (and sometimes of a command). Carries the data the caller needs back.

These are application-level types. They are not domain entities. A `CreateScenarioCommand` is not a `Scenario`. The use case takes the command, builds or loads a `Scenario` aggregate, calls a domain method, and the result of the operation may include a `ScenarioId` or a projection of the scenario state, not the aggregate itself.

Why this separation:

- The domain stays free to model itself in business terms without being shaped by transport concerns.
- The command/query types can be versioned independently of the domain model.
- Different external entry points can build the same command from different inputs and call the same use case. The kind and shape of those entry points is an architectural decision.

## Transactions

Transaction boundaries belong in the application layer. The use case decides where the transaction starts and ends.

- The use case opens a transaction.
- All persistence operations (and event-bus writes, when using a transactional outbox) happen inside that transaction.
- The transaction commits.
- After commit, the use case dispatches the events that were recorded.

The domain does not know about transactions. Persistence collaborators may participate in a transaction, but the boundary is the use case's decision.

When a use case touches more than one aggregate, it must decide:

- **Eventual consistency** between them. Each aggregate commits in its own transaction; events propagate the change. This is the default in DDD.
- **Single transaction across both.** Sometimes acceptable when the aggregates are in the same store and the operation is genuinely atomic. Often a sign that the aggregate boundary is wrong.

## Validation

Three kinds of validation, three places:

- **Structural validation.** "Is the input a valid shape?" Happens at or before the application layer (typically in the entry point or in the command parsing).
- **Application validation.** "Does the input refer to things that exist?" Lives in the application layer. The use case checks that the IDs in the command resolve to real aggregates.
- **Domain validation.** "Is this operation allowed by the business rules?" Lives in the domain. The aggregate enforces it.

A common error is to put domain validation in the use case ("if the order total is over X, throw"). That rule belongs inside the order aggregate.

## Errors

Three kinds of errors, three meanings:

- **Domain errors.** Raised by aggregates and value objects when an invariant is violated. Subclasses of `DomainError`. The use case lets them propagate; the entry point translates them to its protocol.
- **Application errors.** Raised by use cases when an operation cannot be carried out for application reasons (not found, conflict, duplicate, missing reference). Distinct types from domain errors, because they signify different things.
- **External-system errors.** Raised by external collaborators (database connection failed, third-party API timed out). The application layer either retries them or wraps them into application errors before propagating. The exact handling is an architectural concern.

Each kind has its own handling. Mixing them produces handlers that cannot decide whether the cause was a business rule or a network failure.

## Application services and CQRS

When the project uses CQRS:

- **Commands** go through command handlers. Each command has one handler. The handler returns nothing, or a thin acknowledgement (the new ID, for example).
- **Queries** go through query handlers. Each query has one handler. The handler returns the data shape the caller needs.
- The command side talks to aggregates and dispatches events.
- The query side talks to a read model, which may or may not be the same as the write model.

When the project does not use CQRS, the same use case can handle reads and writes. The line is drawn when the read model becomes meaningfully different from the write model.

CQRS is not a default. See `07-integration-patterns.md` for when it is justified.

## Anti-patterns

- **Use case that contains business rules.** "If order total is over X, set discount to Y". Push the rule into the aggregate.
- **Use case that talks to infrastructure directly.** "It is faster to call the database directly here". Once that happens, the use case is no longer testable in isolation. The remedy is architectural; load the architecture skill in use.
- **Use case with twelve methods.** Each method is a separate use case. Split.
- **Use case that knows about transports.** Status codes, response shapes, headers. The entry point handles those.
- **Generic use case.** "ProcessRequestUseCase". A use case is named after a business operation. Generic names hide what is happening.
- **Application service as a god object.** `OrderService` with thirty methods. Decompose into use cases per operation.
- **Validation everywhere.** Structural validation in the use case, domain validation in the entry point, application validation in the persistence layer. Place each kind where it belongs.

## Output

For each bounded context, the application layer is well defined when:

- The use cases are listed, named after operations.
- For each use case, the command/query and result types are defined.
- The contracts the use cases depend on (repository, event bus, external services, time, identity, notifications) are listed.
- The transaction boundary for each command use case is decided.
- The error types raised are documented.

Without these, the application layer is improvised per use case. How those contracts are wired and where they live is the architecture skill's territory.
