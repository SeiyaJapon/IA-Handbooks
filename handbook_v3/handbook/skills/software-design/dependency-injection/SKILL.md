# Dependency Injection Skill

Use dependency injection (DI) as a technique for **composition, substitution, boundary control, and testability**. DI is not the same as using a DI container; manual constructor injection counts and is often preferable. DI is also not an excuse to hide poor design behind interfaces.

## When to use

Use this skill when:

- The user asks about wiring components, composition roots, or dependency direction.
- A class is hard to test because it constructs its own collaborators.
- A high-level module is tightly coupled to a specific low-level implementation.
- Tests require huge fixtures, deep mocks, or live infrastructure.
- The team is debating manual DI vs a container.
- An ORM client, HTTP SDK, or framework-specific type is appearing in business code.
- A singleton or global is making test isolation hard.

## When not to use

Do not use this skill when:

- The code is a small script or a one-shot tool. DI overhead is not paid back.
- The dependency is trivial and stable (a logger, a time provider) and a sensible default works without injection.
- The function is pure and has no external collaborators.
- The user asked for an architectural decision (use `software-architecture`); the architecture skill decides where the layers are. DI is the technique that holds the dependency direction those architectures require.
- The proposed "fix" introduces interfaces, factories, or runtime wiring that solve no active force.

## Inputs to inspect first

Before recommending DI changes, inspect:

- **Where do collaborators get constructed?** Search for `new X()`, factory calls, framework-managed instantiation.
- **What is the composition root?** A single place that wires the graph (a `main.ts`, an `app.module.ts`, a CLI entry, a Lambda handler). If there is no single root, the wiring is dispersed.
- **What does the test setup look like?** If tests need huge mocks or live services, the production code is likely under-injected or over-coupled.
- **Are there interfaces (ports / Protocols / abstract base classes) between high-level and low-level modules?** If not, DIP is being violated regardless of whether DI is "in use".
- **Is there a container?** If yes, what does it manage (lifecycles, transient vs singleton)? Container behaviour can hide ordering bugs.
- **Are there service locators or global registries?** Usually a smell.

If any of these is unknown, DI judgements are guessing.

## DI is not a container

The most common confusion: "we use NestJS DI / Spring / Guice / Autofac, so we have DI". A container is one way to implement DI; manual constructor injection is another. Both are valid.

- **Manual DI:** the composition root explicitly wires things up. Verbose at the root, transparent everywhere else. Often preferable for clarity.
- **Container DI:** the framework wires by configuration (decorators, modules, scanning). Less code at the root, more magic; ordering and lifecycle bugs are harder to reason about.

Neither is "more DI" than the other. The choice is operational. The architectural property is the same: dependencies are explicit, inverted where the architecture requires, and concentrated in one composition layer.

## Hard rules

- **DI is a technique, not a goal.** It serves composition, substitution, boundary control, testability. If none of those is at stake, DI is over-engineering.
- **Manual DI counts.** Constructor injection in plain code is fine; a container is not required.
- **The composition root is the only place that wires.** Every other class declares dependencies; it does not construct them.
- **High-level modules do not import concrete low-level modules.** They depend on abstractions (ports, Protocols, interfaces).
- **Service locators and global registries are smells.** They hide dependencies and break testability.
- **Hidden runtime wiring (deep container magic) is a cost, not a feature.** Ordering bugs, lifecycle bugs, and "where does this come from?" debugging are real.
- **Do not introduce interfaces for the sake of "swapability".** Each interface is a real cost. Add them when substitution actually exists or is imminent.
- **Over-injection is a signal of SRP violation in disguise.** A class with 12 dependencies is doing many things.
- **Interface explosion is a signal of premature abstraction.** Two implementations is the typical threshold.

## Patterns and anti-patterns

### Constructor injection

The default. Dependencies declared in the constructor signature; the class cannot be constructed without them.

Pros: visible dependencies, immutability, easy to test.

Cons: long parameter lists when the class has many dependencies (often a sign the class is too big).

### Function parameter injection

For pure functions or thin orchestrators: pass dependencies as parameters.

Pros: maximum simplicity; trivially testable.

Cons: not idiomatic for stateful classes.

### Composition roots

The single place that constructs the dependency graph. In a NestJS app, the root module file. In a CLI, the entry point. In a Lambda, the handler module's top-level code (with caching across invocations).

Pros: one place to reason about wiring.

Cons: that one place can grow large; partition by feature module if needed.

### Factories

When construction is non-trivial (multi-step, conditional). Factories live in the composition root or near it; they do not move construction inside business code.

### Service locators

A global or container queried at runtime for dependencies. Hides what a class actually depends on. Smell.

### Container lifecycles

Containers manage instance lifecycles: transient (new every time), singleton (one for the app), scoped (one per request). Misusing these causes subtle bugs (singleton holding per-request state, transient making expensive setup repeat).

### Test doubles

Tests inject fakes/stubs/spies through the same constructor parameters. If tests cannot do this, the production code is under-injected.

## How to work

1. **Identify the composition root.** If there is none, that is the first finding.
2. **Inspect direct construction in non-root code.** `new X()` outside the composition root is a candidate violation.
3. **Inspect dependency direction.** High-level module importing concrete low-level module is a DIP violation; abstract through an interface.
4. **Inspect over-injection.** Classes with many dependencies are likely SRP violations; the right fix may be splitting, not refactoring DI.
5. **Inspect container usage.** If a container is in use, check lifecycles, magic, and ordering risks. If a container is overkill for the project, manual DI may be a clarity gain.
6. **Inspect tests.** Where tests are awkward, the production code is usually the cause.
7. **Recommend the smallest change** that resolves the active force. Often: extract an interface for one specific dependency, move construction to the composition root, replace a singleton with constructor injection.
8. **Refuse to abstract for the sake of abstraction.** Interfaces, factories, and indirection cost. Add them only when forces justify.

## Output

Return findings as:

- **Concrete violations:** named class/file, what it constructs, what depends on what, the active force (testability, dependency direction, lifecycle bug).
- **Recommended changes:** minimal interface extraction, composition-root moves, container cleanup, removal of service locators.
- **Acceptable shortcuts:** trivial dependencies with sensible defaults; small scripts where DI is overhead.
- **Architectural escalations:** when the violation is dependency direction across architectural layers, route to the architecture skill.

## Escalation

- If the dependency direction affects **system boundaries** (layers, contexts, services), load `software-architecture` and the relevant architecture sub-skill (`hexagonal-architecture`, `clean-architecture`, `onion-architecture`).
- If DI is being used to implement **ports/adapters in hexagonal**, load `hexagonal-architecture`. If **boundaries/gateways in clean**, load `clean-architecture`. If **interfaces declared by inner rings in onion**, load `onion-architecture`.
- If the problem is **purely code-level composition** (one class wiring its dependencies), keep it in `software-design` and this skill.
- If the issue is caused by **framework lifecycle or container behaviour** (NestJS modules, Spring scopes), inspect the relevant framework's conventions or the relevant runtime skill if one exists. Container bugs are not DI bugs; they are framework bugs.
- If the over-injection is actually a **single-responsibility violation**, load `solid-principles`. Splitting the class may resolve the symptom faster than refactoring DI.
- If wiring is the symptom and **testability** is the root concern, load `testing-strategy` to verify the test structure benefits from the change.

## What this skill does NOT do

- Decide architecture. DI is a technique used by many architectures; it does not pick one.
- Mandate a container. Manual DI is fine; container DI is fine. The choice is operational.
- Justify interfaces by default. Each interface is a cost. Add when substitution exists.
- Replace SRP analysis. Over-injection is often SRP, not DI.
