# 05. Relationship with Architecture

Design and architecture are related but distinct.

## Architecture decides where; design decides what is inside

- **Architecture:** which components exist, how they relate, where dependencies go.
- **Design:** how each component is shaped internally.

A hexagonal architecture decides that the domain has a port for a repository. A design decides whether the repository is a generic CRUD interface or a domain-named contract with specific methods.

## Design choices inside architectures

In a hexagonal/clean/onion codebase:

- The **domain** (or Entities, or Domain Model) is where DDD shines if the domain is rich.
- The **application** is where use cases are designed. Thin orchestration; design favours single responsibility.
- The **infrastructure** is where adapters live. Design favours one adapter per port, no business rules.

In a layered codebase:

- The business layer is where transaction script, active record, or DDD-on-layered fit.
- The data layer's design favours repository or DAO.
- The presentation layer's design follows MVC (the framework's convention).

In a microservices system:

- Each service has its own internal design.
- Services may use different design methods for their different domains.

## Patterns at architecture vs design level

Some patterns appear at both levels:

- **Repository:** an architectural concept (the abstraction in the core) and a design pattern (how the implementation is shaped).
- **Strategy:** a design pattern usually; sometimes appears as an architectural choice when the strategies are large components.

When in doubt, ask: does this decision change the system's structure (architecture) or the inside of one component (design)?

## When design overruns architecture

A common smell: a design decision is significant enough that it should have been made architecturally.

Example: choosing CQRS inside one component is a design decision. Choosing CQRS as the system's communication style is architectural. The line is whether the choice constrains other components.

When a design decision starts constraining other components, it has become architectural. Promote it explicitly.

## When architecture overruns design

The reverse: an architectural decision dictates internal details of every component, leaving no room for design choices.

Example: an architecture that mandates every component use DDD with full ceremony. The architecture has prescribed design.

Architecture should leave room for components to choose their internal design. Mandating design at the architectural level is over-reach.

## Output

For each significant design decision, the team can answer:

- Is this a design choice or an architectural one?
- Does it constrain other components?
- Where is it documented?
