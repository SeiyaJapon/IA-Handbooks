# SOLID Principles

## Purpose

Evaluate whether code respects the five SOLID principles — and detect violations that make systems hard to change, test, or extend.

## Responsibilities

- Detect SRP violations: classes or functions with more than one reason to change
- Detect OCP violations: code that requires modification instead of extension when adding behavior
- Detect LSP violations: subtypes that break the contract of their base type
- Detect ISP violations: interfaces that force implementors to depend on methods they don't use
- Detect DIP violations: high-level modules depending on concrete low-level implementations
- Assess whether a violation is genuine or a pragmatic tradeoff worth accepting
- Avoid applying SOLID dogmatically — evaluate benefit vs complexity cost

## Instructions

**SRP — Single Responsibility Principle**
- Check: does this class/module have more than one axis of change? (e.g. both business logic and persistence)
- Check: if I change the data format, does it force me to touch the same class as if I change the business rule?
- Signal: class name with "and", "manager", "handler", or "service" doing 5+ unrelated things

**OCP — Open/Closed Principle**
- Check: to add a new variant of behavior, do I have to modify existing code or add new code?
- Check: are there long `if/switch` chains that grow every time a new case is added?
- Signal: a `switch` on a type string/enum in multiple places that must all be updated together

**LSP — Liskov Substitution Principle**
- Check: can every subtype be used wherever the base type is expected without changing behavior?
- Check: does the subtype throw exceptions the base type doesn't, or ignore methods it should implement?
- Check: does the subtype strengthen preconditions or weaken postconditions relative to the base?
- Signal: `if (x instanceof SubType)` checks in code that should be polymorphic

**ISP — Interface Segregation Principle**
- Check: are there interfaces with methods that some implementors leave empty or throw `NotImplemented`?
- Check: does a client import an interface but only use 2 of its 8 methods?
- Signal: fat interfaces where implementations stub out half the methods

**DIP — Dependency Inversion Principle**
- Check: do high-level modules (business logic, use cases) import low-level ones (DB, HTTP, queue) directly?
- Check: are dependencies injected or constructed inside the class?
- Check: are there abstractions (interfaces/ports) between layers, or direct concrete dependencies?
- Signal: `new DatabaseRepository()` inside a service class; `import knex from 'knex'` in a use case

## Heuristics

Treat as stronger concerns when:

- A class changes for multiple unrelated reasons in the same PR (SRP)
- Adding a new feature requires modifying 5+ existing files instead of adding new ones (OCP)
- A subtype throws unexpectedly or silently no-ops inherited behavior (LSP)
- An interface has 8+ methods and implementors stub half of them (ISP)
- Use case or domain service importing a database client, HTTP library, or queue SDK directly (DIP)

Treat as acceptable when:

- Small modules where strict separation would create more files than value
- Early-stage code where the abstraction boundaries are still being discovered
- Simple CRUD where DIP abstraction adds indirection without testability benefit

## Rules

- Do not apply SOLID mechanically — evaluate the benefit against the complexity cost
- A violation is only a problem if it makes the code harder to change, test, or understand
- DIP and SRP are the most impactful in practice — prioritize them when both apply
- ISP violations in small codebases are often acceptable; flag them in shared or public interfaces

## Activity Traceability

🔧 Loading skill: `solid-principles`
