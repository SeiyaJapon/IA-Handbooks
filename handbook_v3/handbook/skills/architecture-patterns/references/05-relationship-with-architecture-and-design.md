# 05. Relationship with Architecture and Design

## Pattern vs Architecture

Architectures decide structure (hexagonal, clean, onion, layered, microservices, EDA, serverless). Patterns are techniques applied within an architecture.

A hexagonal codebase may use CQRS or not. A microservices system may use sagas or not. The architecture is the host; the pattern is the technique.

Picking a pattern does NOT pick an architecture. "We use CQRS" is not an architectural answer.

## Pattern vs Design

Design is the inside of a component (DDD, transaction script, SOLID, GoF design patterns). Architectural patterns are at the architecture level: they constrain how components interact or how state is managed across components.

Some patterns appear at both levels:

- **Repository:** at the architecture level (driven port abstracting persistence) and at the design level (a class with specific methods).
- **Strategy:** primarily a design pattern, but in some systems a "strategy" is large enough to be architectural.

The line: does the pattern constrain the structure of the system (architecture / architectural pattern) or the inside of a component (design)?

## Pattern vs Pattern

Some patterns are siblings:

- CQRS and Event Sourcing: orthogonal but often paired.
- Saga and process manager: same idea, different vocabulary.
- ACL and adapter (architectural): related; ACL is a specific kind of adapter for translating foreign models.

Some patterns subsume others:

- A transactional outbox subsumes "publish after commit" because the outbox guarantees it.

## Pattern vs Framework

Frameworks may implement patterns. The framework is operational; the pattern is the technique.

- A `@nestjs/cqrs` module implements CQRS, but CQRS is not the module.
- AWS Step Functions implements orchestrated sagas, but sagas are not Step Functions.

Picking a framework that supports a pattern does not adopt the pattern; the team must apply it deliberately.

## Where each lives in the handbook

| Topic | Lives in |
|---|---|
| Architectures | `software-architecture/` and its sub-skills |
| Architectural patterns (CQRS, ES, outbox, saga, ACL) | `architecture-patterns/` and its sub-skills |
| Design methods (DDD, transaction script) | `software-design/` and its sub-skills |
| Design patterns (GoF) | `software-design/design-patterns/` |
| SOLID and code-level principles | `software-design/solid-principles/` |
| Frameworks | `infrastructure/` of the codebase, not a skill |

When in doubt, classify the question by category before answering.
