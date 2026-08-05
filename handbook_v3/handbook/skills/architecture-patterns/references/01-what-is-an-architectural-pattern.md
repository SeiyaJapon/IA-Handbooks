# 01. What is an Architectural Pattern

An architectural pattern is a **technique applied within an architecture** to solve a recurring problem.

## Distinction from architecture, design, and paradigm

- **Architecture:** the structure of the system (hexagonal, clean, onion, layered, microservices, EDA, serverless).
- **Architectural pattern:** a technique applied within an architecture (CQRS, Event Sourcing, outbox, saga, ACL).
- **Design pattern:** a code-level technique (Strategy, Factory, Observer, Repository at code level).
- **Design method:** how to model the inside of a component (DDD, transaction script).
- **Paradigm:** code-level approach (OO, functional, procedural).

Architectural patterns sit between architecture (the structure) and design (the code shape). They constrain how some part of the system works without being the whole architecture.

## Examples of architectural patterns

- **CQRS.** Separate read and write models.
- **Event Sourcing.** Events as source of truth for state.
- **Transactional outbox.** Atomic state change + event publication.
- **Saga.** Long-running workflow with compensation.
- **Anti-corruption layer (ACL).** Translate between contexts.
- **Materialised view.** Pre-computed read shape.
- **Repository.** Abstraction over persistence.
- **Strangler fig.** Migration pattern: new component intercepts old's traffic gradually.
- **Backend-for-frontend (BFF).** A backend tailored to one frontend.
- **API gateway.** Single entry point for external clients.

## What patterns are NOT

- **Architectures.** Picking a pattern is not picking an architecture.
- **Frameworks.** A framework may implement a pattern, but the pattern is the technique.
- **Mandatory practices.** Patterns are applied when the problem matches.

## Why the category matters

Patterns are often confused with architectures. "We use CQRS" is offered as the architectural answer. The architecture is whatever structure hosts CQRS; CQRS is a refinement within it.

Keeping the categories separate lets each decision be evaluated independently.

## Output

For each pattern in use, the team can answer:

- What problem does it solve?
- What is the architecture it is applied within?
- Is the pattern a deliberate choice, or accidental?
