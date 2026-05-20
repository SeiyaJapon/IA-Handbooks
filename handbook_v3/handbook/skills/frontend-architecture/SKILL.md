# Frontend Architecture

## Purpose

Evaluate frontend application structure, component design, state management, and module organization for maintainability and scalability.

## Responsibilities

- Review module and feature boundaries
- Assess component responsibility and decomposition
- Evaluate state management approach and data flow
- Review shared module design and coupling
- Assess routing structure and lazy loading strategy
- Evaluate form handling and validation patterns
- Review API integration layer and error handling
- Detect presentation logic mixed with business logic

## Instructions

- Identify the module boundaries and check whether feature code is co-located
- Check component size: does each component do one thing? is it rendering logic only?
- Check state ownership: is state lifted to the right level? is it duplicated?
- Check shared modules: are they generic utilities or feature-specific code that leaked?
- Check API calls: are they in a service layer, not directly in components?
- Check forms: is validation co-located with the form definition? are error states handled?
- Check routing: are feature modules lazy-loaded? are route guards in place?
- Check for business rules in components — they belong in services or the API layer

## Heuristics

Treat as stronger concerns when:

- Business logic in component methods (calculations, transformations, domain rules)
- Direct HTTP calls from components instead of through a service layer
- Global state used for data that is local to a feature
- Shared module importing feature-specific code (inverted dependency)
- Monolithic components with more than one responsibility
- Duplicate state synchronization (same data managed in two places)
- No error handling on API calls in components
- Lazy loading disabled on large feature modules

Treat as acceptable when:

- Small components with trivial state that would over-engineer a service layer
- Shared utilities in a well-defined common module with clear ownership

## Rules

- Business logic must not live in components
- API calls must go through a service layer
- Shared modules must not import from feature modules
- State must have a single owner — no duplicate synchronization

## Activity Traceability

🔧 Loading skill: `frontend-architecture`
