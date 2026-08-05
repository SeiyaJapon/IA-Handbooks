# 10. User Preferences (Francisco)

This document records project conventions for hexagonal architecture in Francisco Pérez's projects at Kintai. These conventions override generic literature when they conflict. They are based on decisions confirmed in working sessions, not assumptions.

When a convention is OPEN (not yet decided), it is marked explicitly. Do not assume; ask.

## Canonical layers

The valid layers are exactly three:

- `domain/`
- `application/`
- `infrastructure/`

`presentation/` is not a layer. HTTP, GraphQL, CLI, queue consumers, schedulers and any other delivery mechanism are driving adapters and live in `infrastructure/` (typically in subfolders `infrastructure/http/`, `infrastructure/cli/`, `infrastructure/messaging/`).

A `presentation/` folder anywhere in the tree is a finding to fix, not a stylistic choice.

## Dependency direction

- `domain/` depends on nothing.
- `application/` depends on `domain/` and on port interfaces.
- `infrastructure/` depends on `application/` and `domain/`.

Inversions of this rule have no exception, including for "convenience" or "just for now". Any inversion is a finding.

## Subfolders inside `infrastructure/`

Group adapters by type only when there is more than one file of that type:

- `infrastructure/http/` when there are multiple HTTP controllers.
- `infrastructure/persistence/` when there are multiple repository implementations or persistence-adjacent files.
- `infrastructure/messaging/` when there are multiple message consumers or producers.
- `infrastructure/clients/` when there are multiple external service clients.

A single file of a kind lives at `infrastructure/`'s root without a subfolder. Subfolders that hold one file are ceremony.

## Empty layers

A layer is created when it has content. Empty `domain/` or `application/` folders with only a README are noise and must be removed. The reverse is also true: do not skip a layer that should have content; do not place use cases in `infrastructure/`.

## Framework coupling

- `domain/` and `application/` are framework-agnostic. No NestJS imports, no decorators from any framework or library inside these layers.
- DI is manual through constructors. `@Injectable`, `@Inject`, `@CommandHandler`, `@QueryHandler`, `@EventsHandler` and equivalents are forbidden in `domain/` and `application/`.
- Framework wiring (DI modules, `app.module.ts`, per-context module files) lives inside `infrastructure/`. Never at the root next to the layer folders.
- This rule applies recursively: it covers the entire subtree of `domain/` and `application/`, not just the top level.

## Layout for projects with multiple bounded contexts

When several bounded contexts coexist in one repository, the project uses **bounded-context-first** layout by default:

- Each context is a top-level folder under `src/` with the suffix `-context` (the same convention used by the DDD skill).
- Each context contains its own `domain/`, `application/`, `infrastructure/`, only those that have content.
- Server-wide infrastructure that does not belong to any single context (the database client shared across contexts, framework wiring) lives in a top-level `infrastructure/` folder, not as a fake context.

Layer-first layout is allowed when the project is small enough not to need bounded contexts, but it is not the default.

Mixing axes (some contexts at the root, plus layer folders at the root) is forbidden and is a must-flag finding.

## Naming

- **Driving ports.** PascalCase, intent-named. `CreateScenarioUseCase`, `EvaluateRiskUseCase`, `ListActiveProductsQuery`. When CQRS is used: `<Command>Handler`, `<Query>Handler`.
- **Driven ports.** PascalCase, capability-named. `OrderRepository`, `RiskScoreProvider`, `EventBus`. With suffix `Interface` when the convention demands it (the project's TS convention does).
- **Driving adapters.** Descriptive of the transport: `<UseCase>Controller` for HTTP, `<UseCase>CliCommand` for CLI, `<Event>Consumer` for queue (the term `consumer` is the canonical name for SQS-based workers and equivalents in this project; do not use `worker`, `listener`, or `subscriber`).
- **Driven adapters.** Descriptive of the technology: `Postgres<Aggregate>Repository`, `Http<Service>Client`, `RabbitMq<EventBus>EventBus`. When there is only one implementation, the technology prefix may be dropped.
- **Generic types are forbidden as ports.** No `Repository<T>`, no `Client<T>`. Each port is named after its specific capability.

## Errors

- Domain errors live in `domain/` and extend `DomainError` (when the project provides such a base).
- The suffix is `*Error` consistently. No `*Exception`.
- Adapters translate technology errors (SDK exceptions, network errors, SQL constraint violations) into the core's error vocabulary. SDK exception types must not propagate to `application/` or `domain/`.

## Transactions

- Transactions are opened and closed in the application layer. The use case decides where the transaction begins and ends.
- Adapters participate in the transaction (they accept the transaction context as a constructor or method parameter), but they do not begin or commit on their own.
- Events are dispatched after the transaction commits, not before.

## Testing

- The application core (`domain/` and `application/`) must be testable without infrastructure. Tests of the core run with in-memory adapters or fakes.
- Integration tests against real infrastructure (database, broker, external API) live in their own test layer and target adapters, not the core.

## Communication style

- Use the term `consumer` for SQS-based workers (and equivalents). Do not use `worker`, `listener`, or `subscriber` as canonical names.
- Avoid em dash in normal prose. When adding an aside or clarification, prefer parentheses or a separate sentence. Do not use em dash as a default punctuation device. Only keep em dash when quoting existing text or when explicitly discussing the character itself.
- Code comments are in English and minimal. Code is expected to be self-explanatory.

## Pattern boundaries

- CQRS, Event Sourcing, and Event-Driven Architecture are not part of hexagonal. They are separate decisions covered by their own skills (or, for now, by `architecture-patterns/`).
- Choosing hexagonal does not commit the project to any of those.
- A hexagonal core is fine without a single domain event, without CQRS, and without external messaging.

## Open conventions (to confirm before treating as binding)

- Whether driving ports use the suffix `UseCase` consistently, or whether `<Command>Handler`/`<Query>Handler` is used when CQRS is in place and `UseCase` otherwise.
- Whether the `Interface` suffix is mandatory on every port, or only on driven ports (the broader project rule says yes for behavioural contracts; verify case by case for hexagonal-specific surfaces).
- Whether there is a strict rule for where `time` and `identity` adapters live (top-level `infrastructure/` shared across contexts, or per-context).
- Whether the composition root must be a single file or whether per-context modules are allowed to wire their own adapters.
- Whether monorepos with multiple contexts must enforce the dependency rule with tooling (lint, dep-graph), or whether code review is sufficient.

These are open. Do not assume; ask before treating any of them as a rule.
