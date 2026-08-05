# 06. Anti-patterns

## Pattern by reflex

A pattern adopted because it is well-known, not because the problem matches.

- "DDD says CQRS, so we use CQRS."
- "Microservices need event sourcing."
- "Outbox is best practice."

Fix: adopt the pattern only with a concrete problem that matches its intent.

## Pattern as architecture

Treating a pattern as the architectural answer.

- "Our architecture is CQRS." CQRS is a pattern; the architecture is the structure that hosts it.

Fix: name the architecture (hexagonal, clean, microservices, etc.) separately from the patterns applied within it.

## Pattern stacking without justification

Adopting CQRS + ES + EDA + sagas + outbox simultaneously for a small project.

Fix: one pattern at a time. Understand each before adopting the next.

## Pattern without operational maturity

Patterns require discipline:

- Outbox: relay must run, monitored.
- Saga: state must persist; compensations tested; observability in place.
- Event Sourcing: schema versioning, snapshots, replay.

Adopting a pattern without the operational machinery produces silent failures.

Fix: only adopt patterns the team can operate.

## Pattern half-applied

CQRS adopted with separate handlers, but no separate read model. Event Sourcing adopted, but events are not the source of truth (state is also persisted, and they diverge). Outbox adopted, but no relay running.

Fix: adopt fully or not at all. Half-applied patterns combine the cost of both with the benefit of neither.

## Pattern frozen

A pattern adopted years ago for forces that no longer exist. The cost is paid; the benefit is gone.

Fix: revisit decisions periodically. Architectural pattern adoption is reversible.

## Saga without compensation

A long-running workflow with no plan for failure. Partial state inconsistency on errors.

Fix: every saga step has a compensation. Compensations are tested.

## Outbox without monitoring

The outbox table grows; the relay is stuck. Events accumulate; consumers miss them.

Fix: monitor outbox depth. Alert on growth. Test the relay's failure modes.

## ACL omitted when needed

A context imports a foreign model directly. The foreign model's vocabulary leaks; changes ripple.

Fix: introduce an ACL at the boundary.

## Repository as generic CRUD

A `Repository<T>` with `find`, `save`, `delete` methods. Generic, persistence-shaped, not domain-named.

Fix: per-aggregate repositories with domain-named methods (`findActiveScenariosFor(customerId)`).

## Output

When reviewing pattern usage, look for these smells. Pattern by reflex, pattern as architecture, and half-applied patterns are the most common.
