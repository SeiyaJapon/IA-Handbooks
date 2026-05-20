# Event Sourcing

## Purpose

Review event sourcing implementations: the event log as source of truth, projection design, snapshot strategy, and operational concerns around replay and schema evolution.

## Responsibilities

- Evaluate event log design and append-only guarantees
- Review event schema design and versioning strategy
- Assess projection correctness and idempotency
- Evaluate snapshot strategy for long-lived aggregates
- Review replay capability and its operational implications
- Assess event store technology choice and durability guarantees
- Detect mixing of event sourcing with direct state mutation
- Evaluate schema evolution and backwards compatibility of events

## Instructions

- Identify the event store: is it truly append-only? is ordering guaranteed?
- Check event schema: are events named in past tense? do they carry enough data to reconstruct state?
- Check projections: are they idempotent? can they be rebuilt from scratch by replaying all events?
- Check snapshots: are they an optimization only? can the system work without them (slower but correct)?
- Check replay: is it tested? what is the time/cost of a full replay in production?
- Check schema evolution: if an event format changes, are old events still processable?
- Check for state leakage: is any state written directly to a mutable store, bypassing the event log?

## Heuristics

Treat as stronger concerns when:

- Events that update existing records instead of appending new ones (not append-only)
- Projections that are not idempotent — replaying causes duplicate or incorrect state
- No schema versioning strategy — old events unprocessable after a schema change
- Snapshots that are required for correctness, not just performance — the log alone is insufficient
- Direct mutable state writes alongside event sourcing — hybrid that defeats the pattern's guarantees
- No replay capability — the event log exists but cannot be used to rebuild state
- Events that are too coarse (one event per batch operation) or too fine (one per field change)

Treat as acceptable when:

- Snapshots required for performance on aggregates with thousands of events — if correctness without them is preserved
- Simplified event schema in early-stage systems where the model is still evolving

## Rules

- The event log is the source of truth — no state change without a corresponding event
- Projections must be idempotent and rebuildable from scratch
- Event schemas must be versioned before any breaking change
- Replay must be tested and the cost must be known

## Activity Traceability

🔧 Loading skill: `event-sourcing`
