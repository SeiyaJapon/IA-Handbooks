# Refactoring Strategy Skill

## When to use

The user asked for refactoring direction: when to refactor, in what steps, with what risk, with what tests.

## When not to use

- Mechanical reformatting: `code-readability` or the formatter.
- Architectural rework that crosses layers or modules: `software-architecture`.
- Refactoring tied to migrating between architectures: `software-architecture/references/05-migration-paths.md`.

## Inputs to inspect first

- The pain (concrete change, repeated bug, slow tests). Refactoring without pain is taste.
- Test coverage on the affected paths.
- Reversibility of the change.
- Available time and ship pressure.

## How to work

1. Identify the active pain.
2. Identify the seam where the refactor lives.
3. Ensure tests protect the behaviour.
4. Refactor in small steps; one behaviour-preserving step at a time.
5. Avoid mixing refactoring with feature work in the same commit.

## Output

A step plan, each step shippable, each step tested.

## Escalation

- Architecture-level rework: `software-architecture` + relevant sub-skill.
- Migration between architectures: `software-architecture/references/05-migration-paths.md`.

---

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