# Refactoring Strategy

## Purpose

Plan safe, incremental refactoring without losing behavior or increasing unnecessary risk.

## Responsibilities

- Identify the reason for refactoring
- Separate behavioral change from structural change
- Detect risky dependencies and coupling
- Propose small, reversible steps
- Preserve existing behavior
- Identify required tests before, during, and after refactoring

## Instructions

- Start from the current pain or design problem
- Identify what must remain behaviorally unchanged
- Prefer small steps over large rewrites
- Avoid mixing refactoring with new features unless explicitly justified
- Use tests to protect behavior before changing structure
- Identify seams where code can be safely isolated
- Prefer incremental migration over big-bang replacement

## Heuristics

Treat as higher-risk refactoring when:

- It touches money, auth, contracts, persistence, or messaging
- It changes public APIs, events, schemas, or shared libraries
- It spans several modules, services, or bounded contexts
- Existing tests are weak or missing
- The current behavior is not well understood

Treat as safer refactoring when:

- Behavior is covered by tests
- Changes are local and reversible
- Interfaces remain stable
- Migration can be done incrementally
- Old and new paths can coexist temporarily

## Rules

- Do not rewrite when incremental refactoring is enough
- Do not mix behavior changes with structural changes unless necessary
- Protect behavior with tests
- Prefer reversible steps
- Make migration risks explicit
- Explain trade-offs when proposing a refactoring path

## Activity Traceability

🔧 Loading skill: `refactoring-strategy`