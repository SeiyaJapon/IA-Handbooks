# 08. User Preferences (Francisco)

Project conventions for architectural patterns in Francisco Pérez's projects at Kintai.

## Default policy

Patterns are NOT defaults. Each pattern adopted is a deliberate decision with a documented reason.

The base architecture is hexagonal (see `software-architecture/hexagonal-architecture/`). The base communication is async (events) for cross-context, sync (published interface) for direct queries. The base persistence is state-based.

Patterns adopted on top of this base require justification.

## Patterns currently in use in this project

- **Transactional outbox** for cross-context event publication. Implemented in `shared-context/`.
- **CQRS** at the shared kernel level (Command/Query/Handler interfaces, in-memory buses). Each context decides whether to apply CQRS internally.
- **Saga (orchestrated)** through Step Functions or hand-rolled saga components for cross-service workflows when the workflow is complex.

## Patterns not in use

- **Event Sourcing.** Not adopted. Domain events are produced and dispatched, but state is persisted directly, not derived from events.
- **API gateway** (as a separate component). Not in use; clients reach services directly or through Databricks app routing.
- **Strangler fig.** Not currently in active use, but reserved for migrations.

## Adoption rules

- New pattern adoption requires a written decision (forces, alternatives considered, reason chosen) recorded somewhere durable.
- Patterns are adopted one at a time; the team operates each before adopting the next.
- Patterns adopted partially are completed or removed; half-applied patterns are not acceptable as steady state.

## Operational requirements

For each pattern in use:

- Monitoring of pattern-specific metrics (outbox depth, saga states, CQRS sync lag).
- Alerts on degradation.
- Documented runbooks for failure scenarios.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether CQRS is applied at the context level (per-context decision) or at the project level (once decided, applies to all contexts).
- Whether sagas default to orchestration (Step Functions) or choreography (events).
- Whether ACLs are formal classes or implicit translations in adapters.

These are open. Do not assume; ask.
