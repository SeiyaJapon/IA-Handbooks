# Architecture Review

## Purpose

Evaluate whether a technical case fits the intended architecture and preserves system maintainability.

## Responsibilities

- Review requirements, designs, code, or proposed changes
- Identify architectural boundaries involved
- Detect coupling, leakage, or unclear ownership
- Evaluate whether responsibilities are placed in the right layer
- Detect unnecessary abstractions or missing abstractions
- Identify maintainability, testability, and evolution risks
- Propose architectural direction when useful

## Instructions

- Start from the available evidence
- Identify the main architectural concern
- Check whether the design respects module, layer, and context boundaries
- Look for business logic hidden in infrastructure, handlers, controllers, or framework code
- Look for infrastructure details leaking into domain or application code
- Distinguish between real architectural problems and acceptable pragmatic shortcuts
- Prefer the simplest design that preserves boundaries and future evolution

## Heuristics

Treat as stronger architecture concerns when the case involves:

- Business rules outside the domain or application layer
- Domain code depending on frameworks, ORMs, SDKs, queues, HTTP, or databases
- Infrastructure concepts driving domain language
- Generic services with unclear ownership
- Shared abstractions used before a real need exists
- Multiple bounded contexts changed for a single use case
- Contract changes without clear ownership

Treat as acceptable when:

- The shortcut is local, explicit, and easy to replace
- The abstraction would add more complexity than value
- The decision keeps business rules clear and testable
- The coupling is intentional and documented

## Rules

- Do not redesign the whole system unless explicitly asked
- Do not reject pragmatic solutions only because they are imperfect
- Explain the architectural trade-off
- Prefer clear boundaries over clever abstractions
- Prefer maintainable code over pattern purity
- Do not invent missing constraints

## Activity Traceability

🔧 Loading skill: `architecture-review`