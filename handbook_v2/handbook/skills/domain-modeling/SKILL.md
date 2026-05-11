# Domain Modeling

## Purpose

Model business concepts clearly using domain language, boundaries, invariants, and behavior.

## Responsibilities

- Identify whether DDD is appropriate for the case
- Identify domain concepts, entities, value objects, aggregates, and services
- Detect missing or weak domain language
- Identify invariants and business rules
- Clarify aggregate boundaries and ownership
- Detect anemic models or misplaced business logic
- Evaluate whether persistence or infrastructure concerns are leaking into the domain

## Instructions

- Start from the business problem, not from persistence or framework details
- Use the ubiquitous language from `handbook/memory/glossary.md` when available
- Identify the main business capability involved
- Assess whether the case has enough domain behavior, invariants, lifecycle, or business language to benefit from DDD
- Identify entities only when identity and lifecycle matter
- Prefer value objects for concepts defined by their attributes
- Keep aggregates small and focused on consistency boundaries
- Put invariants close to the model that owns them
- Avoid modeling database tables as domain objects by default

## Heuristics

Treat as stronger domain modeling concerns when:

- Business rules are implemented in controllers, handlers, consumers, or repositories
- Entities are just data containers without behavior
- Domain names come from infrastructure instead of business language
- One aggregate owns too many unrelated invariants
- Persistence shape dictates the model
- Events describe technical actions instead of business facts

Treat as acceptable when:

- A simple data structure is enough and no behavior exists yet
- A concept does not need identity or lifecycle
- A rule belongs to application orchestration rather than domain behavior
- The model is intentionally simple and can evolve later

## Rules

- Evaluate whether DDD is appropriate before applying tactical DDD patterns
- If DDD is appropriate, prefer explicit domain language and protect domain behavior
- If DDD is not appropriate, avoid unnecessary tactical ceremony
- When using DDD, choose entities, aggregates, and value objects based on identity, lifecycle, invariants, and business meaning
- Keep domain code independent from infrastructure
- Explain modeling trade-offs when relevant
- Do not invent domain rules not present in the case

## Activity Traceability

🔧 Loading skill: `domain-modeling`