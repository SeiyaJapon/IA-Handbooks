# Clean Architecture Skill

Use this skill when designing, reviewing, or refactoring software using Clean Architecture as defined by Robert C. Martin (Uncle Bob, 2012). Triggers: clean architecture, four circles, Entities, Use Cases, Interactor, Boundary, Interface Adapters, Frameworks & Drivers, Use Case Boundary, Input Port, Output Port, Presenter, View Model.

## What clean is

Clean Architecture was introduced by Robert C. Martin in 2012 in a blog post and expanded in the book "Clean Architecture: A Craftsman's Guide to Software Structure and Design" (2017). It is Martin's synthesis of earlier inward-dependency architectures into a four-circle model unified by **The Dependency Rule**.

The four circles, from innermost to outermost:

1. **Entities** : enterprise-wide business rules.
2. **Use Cases** : application-specific business rules. Implemented as **Interactors** that talk to the outside through **Use Case Boundaries** (Input Boundary, Output Boundary).
3. **Interface Adapters** : Controllers, Presenters, Gateways. Translate between the inner circles and outer formats.
4. **Frameworks & Drivers** : the framework, database engine, web server, devices.

The Dependency Rule: **source code dependencies can only point inward**.

## What clean is and is not

Clean is a **system architecture**, sibling of hexagonal and onion. The three share the inward-dependency principle but differ in vocabulary, granularity, and the artefacts they prescribe.

Clean is NOT:

- Hexagonal. Hexagonal speaks of ports and adapters; clean speaks of **boundaries**, **interactors**, and **interface adapters with named roles** (Controller, Presenter, Gateway). Pick one.
- Onion. Onion uses rings and explicitly separates Domain Services and Application Services; clean folds those into Use Cases. Pick one.
- A folder template. The Dependency Rule is what makes it clean.
- A design method. Entities and Use Cases are circles, not modelling instructions.
- DDD. Clean is the structure; DDD is the modelling inside the inner circles.

## Vocabulary discipline

This skill uses **Uncle Bob's vocabulary** consistently:

- "Circles", not rings (onion) or regions (hexagonal).
- "Use Case Interactor" or "Interactor" for the class that implements a use case.
- "Input Boundary" and "Output Boundary" for the interfaces that protect the Use Case from the outer circle.
- "Controller", "Presenter", "Gateway" for the three roles in Interface Adapters.
- "View Model" for the data structure passed from Use Case to Presenter.

Do not import vocabulary from hexagonal ("ports", "driving/driven adapters") or onion ("rings", "Domain Services as a separate ring") into clean discussions. They are sibling architectures with different vocabularies; mixing produces confusion without benefit.

## Non-negotiable rule

Never start clean work from folders or frameworks.

The mandatory order is:

1. Identify the **Entities** (enterprise-wide business rules, the most stable code).
2. Identify the **Use Cases** (application-specific business rules).
3. Define the **Use Case Boundaries**: Input Boundary (the interface the Controller calls) and Output Boundary (the interface the Interactor calls to deliver the result).
4. Define the **Gateways** (interfaces the Interactor uses to reach data and external services).
5. Build the **Interface Adapters**: Controllers, Presenters, Gateway implementations.
6. Plug the **Frameworks & Drivers** (database, web, framework) into the Interface Adapters.
7. Only then discuss folders, framework wiring, concrete technologies.

## The Dependency Rule (hard rule)

Source code dependencies point inward only:

- Entities depend on nothing.
- Use Cases depend on Entities.
- Interface Adapters depend on Use Cases and Entities.
- Frameworks & Drivers depend on Interface Adapters (and indirectly on the inner circles).

Inversion happens through **Boundaries**: Use Cases declare Input Boundaries (called by Controllers) and Output Boundaries (called by the Interactor to deliver results to Presenters). The Use Case never imports a Controller, a Presenter, or a Gateway implementation.

## Hard rule about Use Case structure

A Use Case in clean has a specific structure:

- **Input Data** (a plain data structure carrying the Use Case's input).
- **Input Boundary** (an interface, implemented by the Interactor).
- **Use Case Interactor** (the class that implements the Input Boundary).
- **Output Data** (a plain data structure carrying the Use Case's result).
- **Output Boundary** (an interface, implemented by the Presenter).

This is more prescriptive than hexagonal's "use case" or onion's "Application Service". Clean names each artefact.

In simple systems, some teams collapse Output Boundary into a return value. Doing this is a deliberate trade-off; the cost is that the Presenter cannot be substituted at runtime.

## Forbidden shortcuts

- "Clean is just hexagonal."
- "Entities is the domain folder."
- "Use Cases are services."
- "If we have four folders, we have clean."
- "Clean = DDD."
- "Boundaries are just interfaces, no need to name them."
- "Presenter and Controller are the same thing."

If a discussion uses hexagonal or onion vocabulary inside a clean codebase, stop and reframe in clean's vocabulary.

## Mandatory review behavior

When reviewing a clean architecture, check in order:

1. Are the four circles identifiable? (Entities, Use Cases, Interface Adapters, Frameworks & Drivers.)
2. Is The Dependency Rule respected? Does any inner circle import an outer circle?
3. Are Use Cases implemented as Interactors with explicit Input Boundaries (and, where the system warrants, Output Boundaries)?
4. Are Controllers, Presenters, Gateways named and separated, or collapsed into one role?
5. Are Entities free of framework, ORM, HTTP, SDK imports?
6. Are Use Cases free of framework imports?
7. Is vocabulary consistent (no "ports", "rings", "Domain Services" creeping in)?

## When to pick clean over hexagonal or onion

Pick clean when:

- The team's literature is Uncle Bob's books.
- The four-circle vocabulary is the team's lingua franca.
- The explicit Controller / Presenter / Gateway roles add clarity.
- Use Case Boundaries (Input/Output) are valuable for the team's workflow (testing, multiple presentations).

Pick hexagonal when ports and adapters fit better. Pick onion when the explicit Domain Services ring fits better.

The three are siblings. The choice is largely one of vocabulary preference and granularity preference.

## When NOT to pick clean

- Trivial CRUD: overkill.
- Short-lived projects: overkill.
- Teams already speaking hexagonal or onion: pick one. Migrating between siblings is mostly cosmetic; do not do it without reason.

## References

- `references/01-foundations.md` : origin, when clean applies, when it does not.
- `references/02-the-four-circles.md` : Entities, Use Cases, Interface Adapters, Frameworks & Drivers in detail.
- `references/03-the-dependency-rule.md` : The Dependency Rule, inversion through Boundaries.
- `references/04-use-case-structure.md` : Interactor, Input/Output Boundary, Input/Output Data. The artefact prescribed by clean.
- `references/05-interface-adapters.md` : Controllers, Presenters, Gateways. Three named roles, distinct from each other.
- `references/06-frameworks-and-drivers.md` : outermost circle, framework wiring.
- `references/07-relationship-with-other-architectures.md` : clean vs hexagonal vs onion, and why not to mix vocabulary.
- `references/08-anti-patterns.md` : the recurring failures in clean codebases.
- `references/09-review-checklist.md` : operational checklist.
- `references/10-user-preferences.md` : Francisco's project conventions.

Important: do not load hexagonal or onion skills as part of clean work. They are siblings with different vocabularies. If the case crosses into one, finish the clean step first.
