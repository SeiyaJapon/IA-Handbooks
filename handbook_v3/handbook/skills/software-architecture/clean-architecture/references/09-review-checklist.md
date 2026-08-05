# 09. Review Checklist

Operational checklist for reviewing a Clean Architecture codebase, after the cross-architecture review confirmed clean is in use.

The checklist uses clean's vocabulary throughout. If hexagonal or onion terms surface during review, that itself is a finding (vocabulary mixing).

## Step 1: Does clean apply?

1. Is the domain rich enough to justify Entities + Use Cases separation?
2. Is the lifetime long enough to outlive frameworks and ORMs?
3. Does the team's vocabulary speak clean (Uncle Bob's terms)?
4. Are explicit Boundaries (Input/Output) and named adapter roles (Controller/Presenter/Gateway) valuable for this codebase?

**Stop condition.** If two are no, recommend evaluating hexagonal or onion (siblings) or a simpler architecture.

## Step 2: Are the four circles identifiable?

1. Is the **Entities** circle present, with classes that hold enterprise-wide business rules?
2. Is the **Use Cases** circle present, with Interactors per business operation?
3. Is the **Interface Adapters** circle present, with Controllers, Presenters, Gateways?
4. Is the **Frameworks & Drivers** circle present, with framework wiring and drivers?

If any circle is missing or ambiguous, the architecture is implicit. Stop and report.

## Step 3: Is The Dependency Rule respected?

1. Do Entities depend on nothing?
2. Do Use Cases depend only on Entities and Boundary / Gateway interfaces?
3. Do Interface Adapters depend on Use Cases and Entities?
4. Are there inversions (inner depending on outer)?
5. Is the rule enforced (lint, dep-graph, review)?

**Stop condition.** Inversions invalidate the architecture for affected paths. Stop and report.

## Step 4: Are inner circles framework-free?

1. Do Entities import any framework, ORM, HTTP, or SDK type?
2. Do Use Cases import any framework type or decorator?
3. Are there ORM annotations on Entities?
4. Are there framework decorators on Interactors?

## Step 5: Use Case structure (clean-distinctive)

For each use case, check:

1. Is there a **Use Case Interactor** named after the operation?
2. Is there an **Input Boundary** (interface implemented by the Interactor)?
3. Is there an **Input Data** carrying the request?
4. Is there an **Output Boundary** (interface implemented by the Presenter), or is the collapse documented?
5. Is there an **Output Data** carrying the result?
6. Are **Gateway interfaces** declared by the Use Case (not in Interface Adapters)?
7. Is each use case one Interactor (not many use cases bundled)?

## Step 6: Interface Adapters roles

1. Are **Controllers** thin, free of business rules, free of formatting?
2. Are **Presenters** present (or absent with deliberate collapse), implementing the Output Boundary, building View Models?
3. Are **Gateways** implementing the Use Case's Gateway interfaces, free of business rules?
4. Are the three roles distinct, not collapsed into "service"?

## Step 7: Frameworks & Drivers contained

1. Is framework code confined to the outermost circle?
2. Is wiring concentrated in a known location (`main.ts`, `app.module.ts`)?
3. Are there business decisions in the bootstrap?

## Step 8: Vocabulary discipline

1. Is the codebase consistent in clean's vocabulary?
2. Are "ports", "driving/driven adapters", "rings", "Domain Services as a separate ring" appearing?

Vocabulary mixing is itself a finding.

## Summary

After all steps, produce:

- **Top three findings.** Direction violations and framework leakage outrank Use Case structure issues, which outrank role-naming issues, which outrank vocabulary slips.
- **Quick wins.** Add a missing Boundary; rename a generic "service" to its proper role; add a lint rule.
- **Backlog.** Refactor a fat Interactor; remove ORM annotations from Entities; consolidate vocabulary.
- **Confirmed strengths.**

## Forbidden conclusions

- "It is fine because we have four folders."
- "It is fine because we use NestJS / Spring."
- "It is fine because The Dependency Rule is documented."
- "It is fine because we have ports and adapters."

The Dependency Rule, the Use Case structure (Interactor + Boundaries + Data), and the named adapter roles (Controller / Presenter / Gateway) are what make clean. Folders, framework, documentation, and hexagonal-style adapters alone do not validate the architecture as clean.
