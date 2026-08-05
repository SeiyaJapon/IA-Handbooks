# 07. Review Checklist

Operational checklist for reviewing the design of a component or module.

## Step 1: Is the design appropriate for the domain?

1. Is the domain rich (DDD applies)?
2. Is the domain moderate (transaction script or active record)?
3. Is the domain thin (CRUD with validation)?
4. Does the chosen design method match?

## Step 2: SOLID

1. Are classes single-responsibility?
2. Are extensions possible without modifying existing code?
3. Are subtypes substitutable for their base?
4. Are interfaces narrow, not fat?
5. Do high-level modules depend on abstractions, not concretes?

## Step 3: Code readability

1. Are names clear and intent-revealing?
2. Are functions short, doing one thing?
3. Are methods at one level of abstraction?
4. Is the code self-documenting, with comments only where intent is non-obvious?

## Step 4: Patterns

1. Are patterns applied to real problems, not as ceremony?
2. Are there cases that obviously call for a pattern but use a long if-chain?
3. Are abstractions justified by current needs?

## Step 5: Dependency inversion

1. Are dependencies injected through constructors?
2. Are concrete dependencies imported by high-level modules?
3. Are tests possible without infrastructure?

## Step 6: Error handling

1. Are errors typed and propagated explicitly?
2. Are domain, application, and infrastructure errors distinct?
3. Are silent failures absent?

## Step 7: Concurrency

1. Are concurrency concerns identified?
2. Are race conditions, deadlocks, blocking operations handled correctly?
3. Is shared state minimised?

## Step 8: Refactoring readiness

1. Is the code testable?
2. Are seams in place for change?
3. Are smells named (god classes, fat methods, primitive obsession)?

## Summary

Top three findings. Quick wins. Backlog. Confirmed strengths.

## Forbidden conclusions

- "It is fine because we use design patterns."
- "It is fine because SOLID is documented somewhere."
- "It is fine because the tests pass."

The properties are the forces, the methods, the principles, the patterns applied with judgement, the readability, the testability. Patterns and tests alone do not validate the design.
