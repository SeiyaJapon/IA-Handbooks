# DDD Review

## Purpose

Evaluate whether a design applies Domain-Driven Design concepts correctly: bounded contexts, aggregates, value objects, domain services, and ubiquitous language.

## Responsibilities

- Assess bounded context definition and boundaries
- Evaluate aggregate design: root, invariants, and consistency boundaries
- Review value object usage and immutability
- Assess domain service placement and responsibilities
- Evaluate ubiquitous language consistency in code and naming
- Detect anemic domain models (logic in services, not in domain objects)
- Review context mapping and anti-corruption layers at integration points
- Assess domain event design and publication points

## Instructions

- Identify the bounded contexts involved in the change
- Check aggregate roots: do they enforce all invariants for their consistency boundary?
- Check value objects: are they immutable? do they encapsulate validation?
- Check domain services: are they used only for logic that doesn't belong to a single entity?
- Check entity naming: does it reflect the ubiquitous language of the domain?
- Check repositories: do they operate on aggregate roots, not individual entities?
- Check whether domain logic has leaked into application services, controllers, or infrastructure
- Check domain events: are they raised by the aggregate, not by the application layer?

## Heuristics

Treat as stronger concerns when:

- Aggregates with no enforced invariants — all logic in application services
- Value objects implemented as mutable plain objects
- Repositories operating on non-root entities (bypassing the aggregate boundary)
- Domain events published from controllers or infrastructure code
- Naming that reflects database tables or technical implementation, not the domain
- Cross-aggregate references by direct object composition instead of ID
- Business rules duplicated across multiple services instead of encapsulated in the domain

Treat as acceptable when:

- Simple CRUD subdomains without rich domain model — not every context needs full DDD
- Pragmatic aggregate boundaries in early-stage domains where invariants are still being discovered

## Rules

- Do not over-apply DDD — use it where the domain has genuine complexity
- Aggregates enforce their own invariants — not the application layer
- Cross-aggregate communication is by ID, not by object reference
- Ubiquitous language must be consistent from model to code to API

## Activity Traceability

🔧 Loading skill: `ddd-review`
