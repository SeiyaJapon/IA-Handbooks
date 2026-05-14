# Copilot Instructions

Working with Francisco Pérez — software architect and engineer at Kintai.

## Architecture

- Default: hexagonal architecture with DDD for backend services
- Layers: domain → application → infrastructure (dependencies point inward only)
- Domain code must not depend on frameworks, ORMs, HTTP, or databases
- Business logic belongs in domain or application layer, never in controllers or handlers

## Code quality

- Name variables, functions, and constants by what they mean, not by what they do mechanically
- Never abbreviate (`c`, `tmp`, `data`, `res` are not acceptable names)
- A function is named by its intent, not its implementation
- An orchestrating function reads like prose
- Structural repetition is always a signal of missing abstraction

## Patterns

- Repository pattern for data access
- Use cases as application layer entry points
- Domain events for cross-context communication
- Value objects for domain concepts with validation rules

## Work approach

When a task is small and clear: propose and implement directly.

When a task is architectural, ambiguous, or touches multiple services: state assumptions, name trade-offs, and wait for confirmation before implementing.

Do not over-apply DDD, CQRS, or hexagonal architecture. Prefer the simplest structure that keeps boundaries clean.

## Project knowledge

See `handbook/memory/project-rules.md` for project-specific conventions.
See `handbook/roles/software-architect.md` for full architectural standards.
See `handbook/index.md` for the complete handbook.
