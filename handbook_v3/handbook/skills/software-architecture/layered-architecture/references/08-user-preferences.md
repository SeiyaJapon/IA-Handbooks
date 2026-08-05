# 08. User Preferences (Francisco)

This document records project conventions for layered architecture in Francisco Pérez's projects at Kintai. These conventions override generic literature when they conflict.

When a convention is OPEN (not yet decided), it is marked explicitly. Do not assume; ask.

## When layered is acceptable

Layered is acceptable in this project's repos when **all** of the following are true:

- The domain is moderate (validation, simple workflows, no rich invariants).
- The expected lifetime is mid-term (under two years).
- The team owning the codebase is small and the codebase has one bounded context.
- The codebase serves a single driving mechanism (HTTP, or CLI, or queue, but not several sharing logic).

If any of these is false, the default is hexagonal. See `software-architecture/SKILL.md` and the `hexagonal-architecture` sub-skill.

## When layered is rejected

Layered is rejected when:

- The domain is rich. Use hexagonal/clean/onion.
- The lifetime is multi-year. Use hexagonal/clean/onion (the data layer's leakage compounds over years).
- The system has multiple bounded contexts. Layered does not provide context boundaries.
- The system needs multiple driving mechanisms calling the same logic. Use hexagonal.

In these cases, layered is treated as **a starting state to migrate from**, not as a destination.

## Canonical layers

When layered is in use, the three canonical layers are:

- `presentation/` (or framework-default name, e.g. `controllers/` if the framework uses that): HTTP, CLI, GUI, queue consumers (driving entry).
- `business/` (or `services/`, or `application/` if the framework prefers): business rules and orchestration.
- `data/` (or `repositories/`, `dao/`): persistence access.

Variant layers are added only when justified:

- `integration/` when external systems are numerous enough to clutter `data/`.
- `infrastructure/` when cross-cutting concerns (logging, config, metrics) are explicit and shared.

Ceremony layers (`service/` between presentation and business, `application/` inside business) are added only when the responsibility is documented and not just "we wanted another layer".

## Dependency direction

Layered direction is downward and strictly enforced:

- Presentation → Business → Data.
- No skipping (no presentation → data direct).
- No reverse (no data → business, no business → presentation).
- No cycles.

Enforcement is mandatory. Documented-but-not-enforced layered decays into accidental architecture.

## Type isolation

- Database types (ORM entities, rows) do not leak above the data layer.
- Transport types (HTTP request/response) do not leak below the presentation layer.
- The business layer is typed in business terms. If it must accept storage types, that is a flag; consider migrating to hexagonal.

## Framework coupling

- Framework decorators and types are confined to presentation and data layers.
- Business classes take collaborators through plain constructors.
- DI wiring lives in the framework's composition root (typically a presentation-side concern).

## Migration policy

Layered codebases that grow into the conditions where layered no longer fits (rich domain, long lifetime, multi-team) trigger a migration evaluation:

- Evaluate the forces (`software-architecture/references/02-how-to-choose-an-architecture.md`).
- Pick the destination architecture (typically hexagonal).
- Plan the migration (`software-architecture/references/05-migration-paths.md`).
- Use the strangler pattern. Keep the system shippable.

A layered codebase that remains layered past these conditions is architectural debt, not a steady state.

## Communication style

- Avoid em dash in normal prose. When adding an aside or clarification, prefer parentheses or a separate sentence. Do not use em dash as a default punctuation device. Only keep em dash when quoting existing text or when explicitly discussing the character itself.
- Code comments are in English and minimal.

## Open conventions (to confirm before treating as binding)

- Whether the canonical layer names are the literal `presentation/`, `business/`, `data/`, or whether framework-default names are accepted (`controllers/`, `services/`, `repositories/`).
- Whether layered systems in this project must include `integration/` and `infrastructure/` variants by default, or only when justified.
- Whether the migration evaluation is triggered automatically at a specific lifetime (e.g. eighteen months) or only when symptoms appear.
- Whether layered with hexagonal-style folder names (`domain/`, `application/`, `infrastructure/` but downward dependencies) is ever acceptable, or always treated as a migration in progress.

These are open. Do not assume; ask before treating any of them as a rule.
