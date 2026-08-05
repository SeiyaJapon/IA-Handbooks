# 03. When to Apply Patterns

A pattern is applied when:

- A concrete problem matches the pattern's intent.
- The forces justify the pattern's cost.
- The team can operate the pattern's discipline.
- A simpler approach would not solve the problem.

Without those four, the pattern is ceremony.

## Decision process

1. **Identify the problem.** What is the team trying to achieve?
2. **Evaluate the simplest approach.** Could the problem be solved with current tools?
3. **Identify the pattern.** Which pattern matches the problem?
4. **Evaluate the cost.** What does the pattern require operationally and conceptually?
5. **Compare alternatives.** Other patterns that might fit; the no-pattern option.
6. **Decide.** Document the reason where the team will find it later.
7. **Apply.** With the discipline the pattern demands.

## Common forces per pattern

### CQRS

Forces: read/write asymmetry, different scaling, different shapes, multi-source reads. Without these, simpler repository methods suffice.

### Event Sourcing

Forces: history as source of truth, audit, multiple projections, replay. Without these, state-based persistence is simpler.

### Transactional outbox

Forces: events must not be lost between commit and broker publish, broker may be temporarily unreachable. Without these, direct publish is enough.

### Saga

Forces: long-running cross-component workflow, compensation needed for partial failures. Without these, synchronous orchestration or simple events suffice.

### ACL

Forces: foreign model is unstable, vocabulary conflicts with own. Without these, consumer can adopt the producer's model directly.

### Repository

Forces: persistence should be testable in isolation, the design method (DDD, clean) calls for it. Without these, direct ORM access in services may suffice.

### Strangler fig

Forces: migrating from a system that cannot be replaced wholesale, traffic must keep working during migration. Without these, a big-bang rewrite may be acceptable.

## When NOT to apply

- The problem is hypothetical.
- The team does not understand the pattern.
- The operational cost exceeds the benefit.
- The simpler alternative was not seriously considered.

## Anti-patterns in adoption

- **By reflex:** "DDD says CQRS is good." DDD does not require CQRS.
- **Resume-driven:** "I want experience with sagas."
- **Trend-driven:** "Microservices need event sourcing." Most do not.
- **Over-stacking:** CQRS + ES + EDA + sagas + outbox + ACL adopted simultaneously for a small system.

## Output

For each pattern adopted, the team can answer:

- What problem does it solve?
- What forces justify it?
- What was considered as alternative?
- What operational cost does it impose?
- Where is the decision documented?
