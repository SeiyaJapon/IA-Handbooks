# Clean Architecture

## Purpose

Evaluate whether a design follows Clean Architecture principles: independent layers, dependency inversion, and separation of business rules from delivery mechanisms.

## Responsibilities

- Identify the layers present and whether their boundaries are respected
- Detect dependencies pointing in the wrong direction (inward must dominate)
- Evaluate whether use cases are isolated from frameworks, databases, and UI
- Assess whether entities contain only business rules with no infrastructure knowledge
- Review the dependency injection approach and whether it maintains layer independence
- Detect framework or ORM leakage into domain or application layers
- Evaluate testability: can the core be tested without spinning up infrastructure?

## Instructions

- Identify the layers: Entities → Use Cases → Interface Adapters → Frameworks & Drivers
- Check the dependency rule: source code dependencies must point inward only
- Check entities: do they have any import from frameworks, ORMs, HTTP, or database libraries?
- Check use cases: do they depend only on entity interfaces and abstract ports?
- Check controllers/presenters: do they convert between use case data and framework formats?
- Check infrastructure: is it the only place where concrete framework or ORM code lives?
- Ask: can I run the use case tests without a database, HTTP server, or queue?

## Heuristics

Treat as stronger concerns when:

- Entity classes importing ORM annotations, HTTP types, or framework base classes
- Use cases importing Express, AdonisJS, Django, or any delivery framework directly
- Business rules spread across controllers or repositories
- Database schema driving domain model design (anemic domain shaped by DB)
- Dependency injection bypassed — use cases constructing their own infrastructure
- No interface between use case and repository — direct class dependency

Treat as acceptable when:

- Pragmatic shortcuts in small or non-critical modules where the abstraction cost exceeds the benefit
- Use case directly instantiating a value object with no external dependencies

## Rules

- Business rules must be testable without infrastructure
- Dependencies always point inward — no exceptions without documented justification
- Framework code must not leak into use cases or entities

## Activity Traceability

🔧 Loading skill: `clean-architecture`
