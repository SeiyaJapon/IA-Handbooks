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

## Commands

Named workflows available in `handbook/commands/`: `self-review`, `contribute-memory`, `pr-review`

## Project knowledge

See `handbook/memory/project-rules.md` for project-specific conventions and gotchas.
See `handbook/roles/software-architect.md` for architectural standards applied in this project.
