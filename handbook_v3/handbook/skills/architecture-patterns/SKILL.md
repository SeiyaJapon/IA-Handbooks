# Architecture Patterns Skill

Use this skill when discussing, evaluating, or applying patterns that operate **inside** an architecture: CQRS, Event Sourcing, transactional outbox, saga, anti-corruption layer, repository, materialised view, and similar.

This skill is the entry point for architectural patterns as a category. It does not replace specific pattern skills (`cqrs-decision`, `event-sourcing`, and others to be added). It governs how those skills are reached and used together.

## What architectural patterns are

Architectural patterns are **techniques applied within an architecture** to solve recurring problems: how to separate read and write models (CQRS), how to use events as source of truth (Event Sourcing), how to publish events atomically with state (transactional outbox), how to coordinate long workflows (saga).

Patterns are NOT architectures. An architecture (hexagonal, clean, onion, layered, microservices, EDA) decides the structure of the system. A pattern is a technique applied within that structure.

## What this skill is for

- Decide when an architectural pattern is justified.
- Compare patterns that solve similar problems.
- Coordinate patterns that compose (CQRS + Event Sourcing, outbox + EDA).
- Review whether a pattern is applied correctly.

When the case is inside one specific pattern, load that pattern's skill directly. This skill is for cross-pattern decisions.

## Non-negotiable rule

Never start architectural pattern work from "let's add CQRS / Event Sourcing".

The mandatory order is:

1. Identify the **problem** the pattern would solve. Without a concrete problem, the pattern is ceremony.
2. Identify the **forces** that justify the pattern: read/write asymmetry, audit requirements, multi-consumer needs, eventual consistency tolerance.
3. Compare alternatives. Could a simpler approach solve the problem?
4. Evaluate the cost of the pattern (operational, learning, ongoing maintenance).
5. Decide. Document the reason.
6. Apply with the discipline the pattern demands.

## Hard rules

### Patterns serve concrete problems

A pattern without a concrete problem is wrong. CQRS without read/write asymmetry, Event Sourcing without history-as-source-of-truth needs, sagas without long-running cross-component workflows: all wrong.

### Patterns compose with architecture, not as architecture

CQRS, ES, outbox, saga: patterns. They live inside an architecture (hexagonal, clean, onion, layered, microservices, EDA). They do not replace the architectural decision.

### One pattern at a time

Stack patterns deliberately. Adopting CQRS + Event Sourcing + EDA + sagas at once for a small project produces a distributed mess.

### Patterns require discipline

Each pattern has its own operational concerns:

- CQRS: synchronisation between read and write models.
- Event Sourcing: schema evolution, snapshots, replay.
- Outbox: relay running, monitored.
- Saga: state, compensation, observability.

Adopt only what the team can operate.

## Forbidden shortcuts

- "We use CQRS because DDD says so."
- "Event Sourcing is just persisting events."
- "Saga is a try/catch across services."
- "Outbox is just a database table."
- "Patterns are good practices; we apply them everywhere."

## Mandatory review behavior

When reviewing an architectural pattern:

1. Is the pattern applied because of a concrete problem?
2. Does the pattern's discipline match the implementation?
3. Are the operational concerns covered (sync, replay, relay, observability)?
4. Could a simpler approach solve the problem?

## References

- `references/01-what-is-an-architectural-pattern.md` for the distinction between architecture, design, and pattern.
- `references/02-patterns-overview.md` for the catalogue covered.
- `references/03-when-to-apply-patterns.md` for the decision process.
- `references/04-pattern-composition.md` for combining patterns deliberately.
- `references/05-relationship-with-architecture-and-design.md` for where patterns sit.
- `references/06-anti-patterns.md` for cargo cult, premature pattern adoption.
- `references/07-review-checklist.md` for reviewing pattern usage.
- `references/08-user-preferences.md` for Francisco's project conventions.

## Sub-skills (specific patterns)

Each specific pattern has (or will have) its own skill:

- `cqrs-decision/`: when CQRS is justified.
- `event-sourcing/`: when Event Sourcing is justified.
- (Others to be added: transactional outbox, saga, ACL, materialised view, repository pattern.)

Each sub-skill is **complete on its own**. Load the sub-skill directly when the question is inside it.

Use this mega-skill when comparing patterns or deciding among them.
