# 09. User Preferences (Francisco)

This document records project conventions for cross-architecture work in Francisco Pérez's projects at Kintai. These conventions override generic literature when they conflict. They are based on decisions confirmed in working sessions, not assumptions.

When a convention is OPEN (not yet decided), it is marked explicitly. Do not assume; ask.

For architecture-specific preferences (hexagonal layers, naming, framework coupling, etc.), see the `10-user-preferences.md` of each sub-skill.

## Cross-architecture rules

- **An architectural decision is always explicit.** No project ships without a documented architecture. "We will figure it out as we go" is not acceptable for any non-throwaway project.
- **Pick one architecture, stay there.** Mixing vocabularies (hexagonal + clean, hexagonal + onion) is forbidden. If the team's literature changes, a deliberate migration is planned (`05-migration-paths.md`); ad hoc mixing is not.
- **The framework is not the architecture.** NestJS, Spring, Laravel and any other framework live inside the architecture, not above it. Statements like "the architecture is NestJS" are rejected.
- **Design methods, patterns, and paradigms are not architectures.** DDD is design. CQRS, Event Sourcing, Repository, Outbox are patterns. Functional, OO are paradigms. Each is a separate decision; conflating them with architecture is rejected.
- **`presentation/` is not an architectural layer.** This rule comes from hexagonal but applies to any inward-dependency architecture used in the project. HTTP, CLI, GraphQL, queue consumers, schedulers are driving adapters; they live in infrastructure (or its equivalent in the architecture in use).
- **Forces drive the choice.** Cargo cult, resume-driven, trends-over-forces are rejected. The justification for the architecture is the forces of the system, written down.

## Default architecture for backend services

Unless forces dictate otherwise:

- **Single-service backend with rich domain and long lifetime**: hexagonal architecture.
- **Multi-team backend with multiple bounded contexts**: hexagonal per context, plus microservices when the forces justify network boundaries.
- **Lambdas / serverless functions**: hexagonal core inside the function, the function handler is a driving adapter.
- **Thin CRUD admin tools**: framework-default layered, no full architecture ceremony.

## Naming and vocabulary

- The architecture in the project is referenced by name in code documentation and reviews. Implicit architecture is rejected.
- Cross-architecture comparisons use the short codes from `04-comparison-matrix.md` (HEX, CLN, ONI, LAY, MVC, MSV, SVL, EDA) when comparing in tables; full names in prose.
- "Architecture" is not used as a synonym for "framework", "design", "pattern", or "paradigm" in any document.

## Migration policy

- Migrations have a destination, a deadline, and a checkpoint.
- The strangler pattern is the default migration approach.
- New features written during a migration go into the new architecture from day one.
- Permanent half-states are not acceptable. A migration that stalls past its checkpoint is reviewed and either re-committed or rolled back.
- Migrations to satisfy fashion (without a force change) are rejected.

## Documentation

- Every project records the chosen architecture in writing: the architecture itself, the forces, the alternatives considered, and the date of the decision. The format is open (a section in README, a dedicated document, etc.); the discipline of recording is not.
- The record is updated when the architecture changes. The history of the architecture is preserved.
- The architecture is referenced from the project's main README or top-level documentation entry point.

## Enforcement

- Dependency rules of the chosen architecture are enforced by tooling when possible (lint rules, dependency graph checks, build-time failures).
- Code review enforces what tooling cannot enforce.
- An architectural violation in a PR is a blocker, not a comment.
- The enforcement plan is part of the architectural decision; an architecture without an enforcement plan is documentation only.

## Communication style

- Avoid em dash in normal prose. When adding an aside or clarification, prefer parentheses or a separate sentence. Do not use em dash as a default punctuation device. Only keep em dash when quoting existing text or when explicitly discussing the character itself.
- Code comments are in English and minimal. Code is expected to be self-explanatory.
- Architectural conversations use precise vocabulary. "Architecture" is not used loosely.

## Authorisation to act

- Reading files anywhere under `/Users/franciscoperez/` is allowed without asking.
- Creating, editing, and moving files within the project is allowed without asking.
- Git operations that alter history or remote state (`commit`, `push`, `pull`, `merge`, `rebase`, `squash`, `reset --hard`, `branch -D`) require explicit confirmation. A generic "do it" or "go ahead" does not authorise them.
- Commit and push are separate confirmations.

## Open conventions (to confirm before treating as binding)

- Whether the architecture record follows a specific template or remains free-form.
- Whether the project's main architecture document lives at `docs/architecture.md`, in the README, or in a dedicated handbook entry.
- Whether the enforcement tooling for dependency rules is project-wide standardised (one tool for all repos) or chosen per repo.
- Whether the migration checkpoints are time-based (every 3 months) or milestone-based (after each context migrated).
- Whether sibling architectures (hexagonal vs clean vs onion) can coexist in the same monorepo across different services, or whether the whole monorepo must use one vocabulary.

These are open. Do not assume; ask before treating any of them as a rule.
