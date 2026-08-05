# 01. What is Software Design

Software design is the **decision about how the inside of a component or module is shaped**: how its classes are organised, how its functions decompose, what patterns and principles guide the structure.

Design operates at the code level. It is not architecture, not patterns of architecture, not paradigms.

## What design is

- How a single component is modelled internally.
- How responsibilities are split into classes and functions.
- How dependencies between classes are organised.
- How errors propagate.
- How concurrency is expressed.
- How the code is named and structured for readability.

## What design is not

- **Architecture.** The structure of the system, where components live, how they depend (see `software-architecture/`).
- **Patterns of architecture.** CQRS, Event Sourcing, EDA (see `architecture-patterns/`).
- **Paradigms.** OO, functional, procedural (code-level approach, but broader than design).
- **Frameworks.** Wiring conventions.

## Why the distinction matters

Architecture decides what components exist; design decides what is inside them. Confusing the two produces:

- "Designs" that are actually architectural choices ("we will use CQRS").
- Architectural debates that are really design problems ("our service has fat use cases").

Keeping the levels separate lets each be evaluated on its own.

## Design forces

Design choices respond to forces:

- **Domain richness.** Rich domains benefit from DDD; thin ones do not.
- **Change frequency.** Frequent change favours modularity and refactorability.
- **Testing requirements.** Strict testing favours dependency inversion and pure functions.
- **Team familiarity.** Patterns the team does not know are not yet appropriate.

## When design discipline matters most

- Rich domains where business behaviour is non-trivial.
- Code that will live a long time.
- Multiple developers contributing.
- Code that must be testable in isolation.

## When design discipline matters less

- Throwaway scripts.
- Prototypes for one developer.
- Glue code with no business behaviour.

In these cases, the simplest correct code is enough.

## Output

For each component, the team can answer:

- What is its design (DDD, transaction script, active record, etc.)?
- What principles guide it (SOLID, readability)?
- What patterns are used and why?
- What is left intentionally simple?
