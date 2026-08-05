# 10. User Preferences (Francisco)

This document records project conventions for Francisco Pérez at Kintai. These conventions override generic DDD literature when they conflict. They are based on decisions confirmed in working sessions, not assumptions.

When a convention is **OPEN** (not yet decided), it is marked explicitly. Do not assume; ask.

## Bounded context naming

- Folders that represent a bounded context use the suffix `-context`.
  - Examples: `auth-context/`, `product-context/`, `payment-context/`, `receivables-simulator-context/`.
- The shared kernel uses the same suffix: `shared-context/`.
- The suffix is a visual reminder that the folder is a context boundary, not a technical module.

## Communication between contexts

- Direct imports between contexts are forbidden, even within the same monorepo.
- The two valid mechanisms are:
  - **Domain / integration events** (asynchronous, default).
  - **Published SDK / interface** (synchronous, when an event does not fit).
- A monorepo is operational convenience. The boundary is treated as if each context lived in a separate repository.

## Architectural conventions

Layer naming, layout (folders, subfolders, where things live), framework coupling rules, and the dependency direction are **architectural decisions**, not DDD ones. They live in the `10-user-preferences.md` of the architecture skill in use:

- For hexagonal: `software-architecture/hexagonal-architecture/references/10-user-preferences.md`.
- For clean: `software-architecture/clean-architecture/references/10-user-preferences.md` (when consolidated).
- For onion: `software-architecture/onion-architecture/references/10-user-preferences.md` (when consolidated).

Do not look here for `presentation/` rules, `infrastructure/` subfolder grouping, framework-agnostic layers, decorator bans, or DI conventions. Those belong to the architecture, not to DDD.

## Aggregate base

- Aggregates extend `AggregateRoot` from `shared-context/`.
- The base provides:
  - A private list of recorded domain events.
  - `record(event)` to append.
  - `pullDomainEvents()` to drain after persistence.
- The aggregate never publishes its events directly. The application service pulls them after save and dispatches them through the event bus contract (the exact shape and placement of that contract is an architectural concern).

## Domain events

- Class extending `DomainEvent` from `shared-context/`.
- Required fields: `eventName`, `aggregateId`, `aggregateType`, `occurredOn` (provided by the base), and `toPayload()` returning the data subscribers need.
- Past-tense name in the ubiquitous language. `ScenarioCreated`, `ProductMapped`, `RiskScoreRecalculated`. Never `CreateScenario` (command), never `ScenarioCreator` (service), never `OnScenarioCreate` (handler).
- Recorded by the aggregate. Never by the application service or the controller.
- Immutable.

## Errors

- All domain errors extend `DomainError` from `shared-context/`.
- The suffix is `*Error` consistently. No `*Exception`.
- A specific error class per invariant violation. Generic `Error` is not thrown from domain code.
- Domain errors, application errors, and external-system errors are distinct types with distinct handling. (How external-system errors are produced and translated is the architecture skill's territory.)

## Naming conventions inside a context

- **Aggregate roots, entities, value objects.** PascalCase, business name. `Scenario`, `ProductCatalogEntry`, `Money`. Never named after technical concepts (`OrderRecord`, `ProductRow`).
- **Domain events.** PascalCase past tense. `ScenarioCreated`, `ProductMapped`.
- **Domain services.** PascalCase, role-named. `RiskScoreEvaluator`, `EligibilityChecker`. Not `Helper`, `Manager`, `Service` as generic suffix.
- **Use cases / application services.** PascalCase, intent-named. `CreateScenarioUseCase`, `EvaluateRiskUseCase`. Or, when CQRS is used: `<Command>Handler`, `<Query>Handler`.
- **Repository contracts.** `<Aggregate>RepositoryInterface`. The naming of implementations (`<Aggregate>.repository.ts`, `<Adapter><Aggregate>.repository.ts`) is an architectural concern; see the architecture skill in use.
- **Other application contracts.** `<Capability>PortInterface` or `<Capability>Interface` when DDD's needs surface in code (event bus, identity, time). Their physical shape is architectural.
- **Interfaces vs classes.** `interface` is reserved for behavioural contracts, with the `Interface` suffix. Data carriers (`Command`, `Query`, `QueryResult`, `DomainEvent`, DTOs) are abstract classes, not interfaces.

## Comments and documentation

- Code comments are in English and minimal. Code is expected to be self-explanatory.
- A comment is justified only when it explains a non-obvious WHY (a workaround, a hidden constraint, an invariant the code cannot express by itself).
- No multi-paragraph docstrings. No multi-line comment blocks.
- README files are written only when they document something that the names and code do not already convey.

## Writing style

- Avoid em dash in normal prose.
- When adding an aside or clarification, prefer parentheses or a separate sentence.
- Do not use em dash as a default punctuation device.
- Only keep em dash when quoting existing text or when explicitly discussing the character itself.

## Ubiquitous language

- The business speaks Spanish. The team is in Spain (Comunidad Valenciana). Avoid latinoamerican calques and English calques.
- Code identifiers are in English. The translation between business Spanish and code English is documented in `handbook/memory/glossary.md` when ambiguity arises.
- Inside a single context, a term has one meaning. If a term needs to mean two things, that is a sign of a missing context split.

## Pattern boundaries

- CQRS, Event Sourcing, and Event-Driven Architecture are not default DDD steps. They are introduced only when the domain model and the operational requirements justify them, and the reasons are documented.
- The fact that the project uses a CQRS shared kernel for some contexts does not mean every context must use CQRS. Each context decides per its own asymmetry.

## Design discipline

- Strategic DDD comes before tactical DDD. Subdomains, bounded contexts, and the context map are decided first. Aggregates, repositories, and use cases come later.
- Bounded contexts are never treated as folders. They are semantic boundaries; the folder layout is a consequence, not the source.
- Architecture work never starts from the folder layout. It starts from the domain.
- Clarity of the domain takes priority over use of patterns. A clear model with simple infrastructure beats a fashionable architecture wrapped around a confused model.
- CQRS, Event Sourcing, and Event-Driven Architecture are not introduced unless there is a concrete reason. The reason is written down. "Because DDD" is not a reason.
- Hexagonal architecture protects the domain. It does not substitute for modelling the domain.
- Persistence may stay as a TODO when the case does not require it yet. A bounded context can ship without a repository if its current scope does not demand persistence.
- Overengineering is rejected. When a simpler approach fits, the simpler approach wins.
- Be explicit about what is infrastructure and what is domain. When the boundary is ambiguous, name it before continuing.
- The term `consumer` is used for SQS-based workers (and equivalents). Other terms (`worker`, `listener`, `subscriber`) are not the canonical name in this project.
- Repositories are not created by default for everything. A repository exists when there is an aggregate root that needs to be persisted; not for every entity, not for every read.
- Aggregates are never created from database tables. They are created from invariants. The schema is a consequence of the aggregate, not its source.
- Domain rules are not invented. If a rule is not present in the case, it is not added. Modelling describes the business; it does not extend it.

## Authorization to act

- Reading files anywhere under `/Users/franciscoperez/` is allowed without asking.
- Creating, editing, and moving files within the project is allowed without asking.
- Git operations that alter history or remote state (`commit`, `push`, `pull`, `merge`, `rebase`, `squash`, `reset --hard`, `branch -D`) require explicit confirmation. A generic "do it" or "go ahead" does not authorise them.
- Commit and push are separate confirmations.

## Open conventions (to confirm before treating as binding)

The following points have not been confirmed in working sessions. Ask before applying them as rules.

- Whether aggregates expose **getters** for their internal state, or only **query methods** that answer specific business questions.
- Whether **identity types** for aggregates are always value objects (`ScenarioId` wrapping a UUID) or sometimes raw strings.
- Whether **commands and queries** are classes (preferred per the existing convention for data carriers) or accept being plain interfaces in low-stakes use cases.
- Whether **read models** for query-side use cases live in the same context as the write model, or in a dedicated read-side module per context.
- Whether **integration events** are explicitly different classes from **domain events**, or whether the same class is reused at the boundary (with the trade-off documented).
- Whether **factory classes** are used at all, or whether all aggregate construction goes through static factory methods on the aggregate root.
- Whether **error classes** carry structured payloads (codes, parameters) or only messages.

These are open. Do not assume; ask before treating any of them as a rule.
