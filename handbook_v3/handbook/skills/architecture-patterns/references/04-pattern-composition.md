# 04. Pattern Composition

Patterns often compose. Knowing which compose well and which conflict is part of architectural pattern discipline.

## Common compositions

### CQRS + Event Sourcing

Frequent pairing. Write side persists events; read side maintains projections. Not required (CQRS works with state-based persistence; ES works without CQRS), but they fit naturally.

### Transactional outbox + EDA

The outbox guarantees events reach the broker after commit. EDA uses events for inter-component communication. The outbox is the producer-side reliability mechanism for EDA.

### Saga + EDA

Sagas coordinate workflows across components; EDA carries the events. Choreographed saga + EDA = workflow emerges from event chains.

### ACL + EDA

When a context consumes events from another, an ACL translates the foreign vocabulary at the boundary.

### Materialised view + CQRS

The CQRS read side is often a materialised view derived from write events.

### Repository + DDD

Repositories abstract persistence in DDD codebases.

## Conflicts

### CQRS without justification + simple CRUD

Adopting CQRS for a simple CRUD is overhead. Repository methods are enough.

### Event Sourcing without CQRS, in a complex domain

Possible but uncommon. ES typically pairs with CQRS to manage read-model derivation.

### Multiple consistency models in the same flow

Sync within an aggregate, eventual across aggregates, sync between two aggregates "for performance". Complex; hard to reason about. Pick a model per boundary.

## Stacking discipline

Adopt patterns one at a time:

1. Establish the architecture.
2. Add the simplest pattern that fits the most pressing problem.
3. Operate it for a while; understand its cost.
4. Add the next pattern when justified.
5. Repeat.

A team that adopts CQRS + ES + EDA + sagas + outbox simultaneously, before any has been operated, multiplies operational cost without learning.

## Anti-patterns in composition

- **Stacking by reflex.** "Modern DDD systems use ES + CQRS + EDA." Without specific forces, it is cargo cult.
- **Pattern as architecture.** "We use CQRS, that is our architecture." CQRS is a pattern; the architecture is what hosts it.
- **Inconsistent application.** CQRS in one bounded context, none in another, with no clear reason. Can be valid; needs justification.
- **No documentation.** Patterns adopted with no record of the reason. New contributors discover them by reverse engineering.

## Output

For each composition the team adopts, ask:

- Why this combination?
- What is the cost?
- Is each pattern individually justified, or is the combination justifying each?

If the combination justifies each, the patterns may not stand on their own; reconsider.
