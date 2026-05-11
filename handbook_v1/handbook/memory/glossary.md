# Glossary

Domain and architecture terms used across the handbook and the codebase.

---

## Bounded Context

A explicit boundary within which a domain model applies. Outside the boundary, the same term may mean something different. Each service or module is typically one bounded context.

## Use Case (Application Service)

A class or function that orchestrates a single business operation. Lives in the application layer. Coordinates domain objects and infrastructure ports without containing business logic itself.

## Port / Adapter

A port is an interface that the domain exposes or requires. An adapter is the concrete implementation that connects the port to the outside world (HTTP, database, external API). Core concept in hexagonal architecture.

## Domain Event

A record that something meaningful happened in the domain. Named in past tense. Immutable. Published after a state change; consumed by other parts of the system that need to react.

## Value Object

An immutable domain concept defined entirely by its value, not by an identity. Two value objects with the same value are interchangeable. Examples: `Email`, `Money`, `Address`.

## Aggregate

A cluster of domain objects treated as a single unit for data changes. Has a root entity that controls access. Consistency is enforced within the aggregate boundary.

## ADR (Architecture Decision Record)

A short document that captures a significant architectural decision: context, what was decided, and consequences. Stored in `memory/architecture-decisions.md`.