# 04. When to Apply Patterns

Design patterns (GoF and others) are reusable solutions to recurring problems. They are tools, not goals. Applied right, they reduce complexity. Applied wrong, they add ceremony without benefit.

## When patterns apply

A pattern fits when:

- The problem genuinely matches the pattern's intent.
- The complexity of applying the pattern is justified by the benefit.
- The team understands the pattern.
- The pattern produces code that is clearer than the alternative.

## When patterns do not apply

A pattern does not fit when:

- The pattern is applied "in case" without a current need.
- The pattern is applied because it is well known, not because it solves the problem.
- The pattern adds complexity disproportionate to the benefit.
- The team does not know the pattern (apply only after learning).

## Common patterns and when

### Strategy

When behaviour varies along an axis and the variations are significant. Replaces large `if` chains.

### Factory

When creation is non-trivial: multiple steps, dependencies, conditional construction.

### Observer / Pub-sub

When one component's state change must notify many others.

### Adapter

When integrating with code that does not match the desired interface.

### Decorator

When behaviour is added to objects dynamically.

### Repository

When persistence access should be abstracted from business logic. Typical in DDD codebases.

### Command

When operations are first-class objects (queued, logged, undone).

### Specification

When predicates over the domain are first-class (composable, testable, expressive).

## Pattern overuse

Common smells:

- A factory for everything.
- An interface for every class.
- An observer per attribute change.
- A strategy for every two-line variation.

These are signals that patterns are applied as ceremony, not as solutions.

## Pattern starvation

The opposite smell: code that obviously calls for a pattern but does not use one. Long if-chains where strategy would clarify; deep coupling to concretes where DI would help.

## Choosing

Apply a pattern when:

1. The problem matches the pattern's intent.
2. The benefit (clarity, reduced coupling, testability) is concrete.
3. The cost (ceremony, indirection, learning) is justified.

Without those, prefer the simplest direct code.

See `software-design/design-patterns/` for full coverage of common patterns.
