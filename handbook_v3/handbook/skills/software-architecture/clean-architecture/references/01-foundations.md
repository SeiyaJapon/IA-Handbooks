# 01. Foundations

## Origin

Clean Architecture was introduced by **Robert C. Martin (Uncle Bob)** in 2012 in the blog post "The Clean Architecture", and elaborated in the book "Clean Architecture: A Craftsman's Guide to Software Structure and Design" (2017). Martin synthesised earlier inward-dependency architectures (Cockburn's hexagonal, 2005; Jacobson's use case driven development, 1992; Palermo's onion, 2008) into a unified four-circle model bound by a single rule: **The Dependency Rule**.

The contribution of clean is naming and prescribing artefacts that earlier architectures left implicit:

- **Use Case Interactor** as the explicit class that implements a use case.
- **Input Boundary** and **Output Boundary** as interfaces that protect the Interactor from outer-circle code.
- **Controller**, **Presenter**, and **Gateway** as three distinct named roles inside Interface Adapters.
- **Input Data**, **Output Data**, **View Model** as the data structures that flow between circles.

These names are part of clean's identity. They differ from hexagonal's "ports and adapters" and onion's "rings".

## What clean solves

The same problem hexagonal and onion solve: business logic gets entangled with frameworks, databases, and transports unless an explicit rule keeps them apart. Clean expresses the rule with four circles and the inward direction.

The clean-specific contribution: the explicit naming of artefacts. A clean codebase has Interactors, Boundaries, Presenters; not generic "use cases" and "adapters". The naming is a discipline that pays off in larger codebases by making roles unambiguous.

## What clean does not solve

Same as hexagonal and onion:

- Clean does not prescribe how to model the inside of Entities or Use Cases. That is a design concern (DDD, transaction script).
- Clean does not prescribe deployment (monolith, microservices, serverless).
- Clean does not prescribe communication (sync vs async, EDA).

## When clean applies

Apply clean when:

- The system's lifetime is long enough that frameworks, ORMs, and transports will outlive their first choice.
- The domain has rules that would exist regardless of the current application (Entities) and rules specific to the current application (Use Cases).
- The team values testability of the core without infrastructure.
- The team's vocabulary is Uncle Bob's. The Interactor / Boundary / Presenter naming is comfortable.

## When clean does not apply

- Trivial CRUD: the four circles are overkill.
- Short-lived projects: ceremony without payback.
- Teams already speaking hexagonal or onion: clean's vocabulary is a migration cost, not a benefit.
- Frontends with no significant domain logic: clean's structure is for backends.

## Common misreadings

- **"Clean is just hexagonal with different folder names."** No. Clean prescribes Boundaries explicitly; hexagonal does not. Clean splits Interface Adapters into Controllers / Presenters / Gateways; hexagonal does not. The vocabulary differences reflect different prescriptive intent.
- **"Entities is the domain folder."** Entities is a circle, defined by what lives in it (enterprise-wide business rules) and by the rule that nothing inside imports anything outside. The folder name is incidental.
- **"Use Cases are application services."** Application service is hexagonal vocabulary. In clean, the artefact is the **Interactor** with explicit **Input Boundary** and **Output Boundary**.
- **"Clean requires DDD."** No. Clean is the structure; DDD is the inside-the-core modelling.
- **"Boundary just means interface."** Conceptually yes, but the named distinction (Input Boundary, Output Boundary, with the Interactor implementing the Input and the Presenter implementing the Output) is part of clean's prescription.

## Decision

Ask:

1. Does the system have rules independent of the current application (Entities), and rules specific to it (Use Cases)?
2. Is the lifetime long enough to outlive the current framework or ORM?
3. Does the team speak Uncle Bob's vocabulary?
4. Do the explicit Boundary / Controller / Presenter / Gateway roles add value (testing, multiple presentations, large codebase)?

Multiple yeses: clean is a candidate. Compare with hexagonal and onion in `software-architecture/references/04-comparison-matrix.md` and in `references/07-relationship-with-other-architectures.md` of this skill.
