# Design Patterns

## Purpose

Evaluate whether design patterns are applied correctly, necessary, and proportionate — and detect when they add complexity without value.

## Responsibilities

- Identify which patterns are in use, explicit or implicit
- Assess whether the pattern solves a real problem present in the code
- Detect pattern misuse or over-engineering
- Evaluate pattern implementation correctness
- Detect missing abstractions that a pattern would solve cleanly
- Review pattern interactions and combined complexity

## Instructions

- Identify the intent: what problem is this pattern solving?
- Check whether the pattern is implemented correctly for its category (creational, structural, behavioral)
- Check whether the pattern introduces indirection without corresponding benefit
- Check whether a simpler solution would solve the same problem
- Check for patterns applied preemptively for hypothetical future needs
- Check that the pattern name and intent are consistent in naming conventions

**Patterns to check specifically by category:**

*Creational*: Factory, Abstract Factory, Builder, Singleton, Prototype
- Singleton: is global state genuinely necessary? is it thread-safe?
- Builder: is the object complex enough to justify it, or would a plain constructor suffice?

*Structural*: Adapter, Decorator, Facade, Proxy, Composite, Bridge
- Adapter: is it wrapping a real integration boundary, or wrapping internal code unnecessarily?
- Decorator: are there more than 2-3 layers of decoration — is it becoming a chain of responsibility?

*Behavioral*: Strategy, Observer, Command, Template Method, Chain of Responsibility, State, Iterator, Mediator
- Strategy: is the variation real and expected, or invented?
- Observer: are subscriptions cleaned up? can events cascade uncontrollably?

## Heuristics

Treat as stronger concerns when:

- Pattern applied without a clear problem it solves
- Pattern adds 3+ layers of indirection for a problem solvable in 10 lines
- Singleton used for state that could be passed as a parameter
- Observer with no cleanup — memory leak and uncontrolled side effects
- Factory hierarchy of 5+ classes for 2 variations — not enough variation to justify it
- Pattern named in code but implemented incorrectly
- Multiple patterns stacked on a single class without clear reason

Treat as acceptable when:

- Well-known patterns applied cleanly in the right context
- Pattern used implicitly without explicit naming if the structure is clear
- Simpler implementation chosen over a formal pattern — pragmatism over purity

## Rules

- A pattern must solve a real, present problem — not a hypothetical future one
- Indirection must pay for itself in clarity, flexibility, or testability
- Do not reject a pattern because it is "too formal" — reject it if it adds complexity without return

## Activity Traceability

🔧 Loading skill: `design-patterns`
