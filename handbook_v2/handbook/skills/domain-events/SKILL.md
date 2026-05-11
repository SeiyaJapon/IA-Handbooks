# Domain Events

## Purpose

Evaluate, design, and validate domain events as representations of meaningful business facts.

## Responsibilities

- Identify whether a domain event is appropriate
- Ensure the event represents a business fact, not a technical action
- Validate event naming and semantics
- Identify timing and consistency boundaries
- Detect coupling introduced by events
- Evaluate whether the event improves or harms the design
- Recommend event usage when useful

## Instructions

- Start from the business behavior, not from messaging or infrastructure
- Identify what meaningful business fact occurred
- Ensure the event is named in past tense and reflects that fact
- Check whether the event belongs to the domain or is just technical signaling
- Identify when the event is emitted (transaction boundary, eventual consistency, etc.)
- Evaluate who should react to the event and whether that creates coupling
- Consider whether an event simplifies or complicates the design

## Heuristics

Treat as strong domain events when:

- They represent a completed business action
- They express something meaningful in the domain language
- They enable decoupled reactions in other parts of the system
- They align with aggregate boundaries

Treat as weak or problematic events when:

- They describe technical steps (e.g. "entity_saved", "db_updated")
- They leak infrastructure or persistence details
- They are used to replace direct calls without real decoupling benefit
- They introduce unclear timing or ordering issues
- They create hidden dependencies between modules or services

## Rules

- Events must represent business facts, not technical operations
- Prefer explicit domain language
- Avoid events that only mirror CRUD actions
- Make timing and consistency expectations explicit
- Do not introduce events if they do not improve the design
- Explain trade-offs when introducing or rejecting events

## Activity Traceability

🔧 Loading skill: `domain-events`