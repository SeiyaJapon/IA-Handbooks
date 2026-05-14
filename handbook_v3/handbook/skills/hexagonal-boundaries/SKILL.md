# Hexagonal Boundaries

## Purpose

Keep business logic isolated from frameworks, infrastructure, delivery mechanisms, and external systems.

## Responsibilities

- Identify domain, application, infrastructure, and delivery concerns
- Detect boundary leaks between layers
- Identify ports needed by the application or domain
- Identify adapters that implement those ports
- Detect business logic placed in handlers, controllers, consumers, repositories, or framework code
- Evaluate whether dependencies point in the right direction

## Instructions

- Start by identifying the business use case
- Separate what the system does from how it is delivered or persisted
- Keep domain logic independent from frameworks, ORMs, SDKs, HTTP, queues, databases, and cloud services
- Put orchestration in application use cases
- Use ports for external capabilities needed by the application
- Implement ports in infrastructure adapters
- Keep delivery adapters thin

## Heuristics

Treat as stronger boundary concerns when:

- Domain code imports infrastructure, framework, ORM, SDK, or HTTP dependencies
- Controllers, handlers, or consumers contain business rules
- Repositories contain domain decisions instead of persistence logic
- External API models are used as domain models
- Database schemas dictate domain behavior
- Events, commands, or DTOs are reused across unrelated boundaries
- Application use cases know too much about infrastructure details

Treat as acceptable when:

- A delivery adapter only maps input and calls a use case
- An infrastructure adapter only translates between external systems and internal ports
- DTOs are explicitly kept outside the domain
- A pragmatic shortcut is local, visible, and easy to replace

## Rules

- Dependencies must point inward
- Domain must not depend on infrastructure
- Application must depend on abstractions, not concrete adapters
- Infrastructure implements ports; it does not define the business model
- Delivery mechanisms must not own business rules
- Prefer explicit boundaries over implicit coupling
- Explain trade-offs when a boundary shortcut is proposed

## Activity Traceability

🔧 Loading skill: `hexagonal-boundaries`