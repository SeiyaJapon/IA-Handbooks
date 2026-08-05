# 01. Foundations

## Origin

Onion Architecture was introduced by **Jeffrey Palermo** in 2008 in a series of blog posts ("The Onion Architecture: part 1, 2, 3, 4"). Palermo synthesised ideas from Cockburn's hexagonal (2005) and from layered architectures, expressing the result as concentric **rings** around the Domain Model.

The "onion" metaphor emphasises that everything outside the domain depends on the domain, never the reverse. Rings are peeled from outside in: External Layer first, then Application Services, Domain Services, and finally the Domain Model at the centre.

Onion sits between hexagonal (more abstract: ports and adapters, two regions) and clean (more elaborate: four named circles with prescribed Boundaries and adapter roles). Onion's specific contribution is the **explicit separation of Domain Services and Application Services as distinct rings**.

## What onion solves

Same as hexagonal and clean: protect the domain from frameworks and infrastructure. Onion's specific contribution is to make the granularity inside the domain side explicit:

- Domain Model: entities and value objects.
- Domain Services: cross-entity domain behaviour.
- Application Services: orchestration of use cases.

Hexagonal collapses the latter two into "the application core". Clean has Entities and Use Cases without naming a separate Domain Services ring. Onion names Domain Services explicitly as a layer between Domain Model and Application Services.

## What onion does not solve

Same as hexagonal and clean:

- Onion does not prescribe how to model the inside of Domain Model or Domain Services. That is a design concern (DDD, plain OO).
- Onion does not prescribe deployment (monolith, microservices, serverless).
- Onion does not prescribe communication (sync vs async, EDA).
- Onion does not prescribe adapter roles. Adapters live in External Layer; onion does not split them as clean does (Controller/Presenter/Gateway) or as hexagonal does (driving/driven).

## When onion applies

Apply onion when:

- The forces match the inward-dependency family (rich domain, long lifetime, framework-independence valued).
- The domain has **meaningful cross-entity behaviour** that benefits from being named explicitly as Domain Services.
- The team's vocabulary speaks onion (rings, Domain Services as a layer).
- The distinction between domain logic (intrinsic to the business) and application orchestration (specific to this app) is a useful axis to enforce.

## When onion does not apply

- Trivial CRUD: overkill.
- Short-lived projects: overkill.
- Teams already speaking hexagonal or clean: onion's vocabulary is a migration cost, not a benefit.
- Domains without cross-entity behaviour: the Domain Services ring is empty; hexagonal or clean fit better.

## Common misreadings

- **"Onion is hexagonal with different folder names."** No. Same dependency rule, different ring partition. Hexagonal does not separate Domain Services from Application Services; onion does explicitly.
- **"Domain Services is just service classes."** No. Domain Services in onion is a specific ring with a specific responsibility (cross-entity domain logic, intrinsic to the business). Generic services or utilities do not belong in this ring.
- **"Onion requires DDD."** No. Onion is the structure; DDD is the modelling. They compose well because onion's rings align with DDD's tactical patterns, but onion does not require DDD.
- **"Application Services and Domain Services are the same thing."** They are different rings in onion. Confusing them collapses onion into hexagonal or clean.

## Decision

Ask:

1. Are the forces aligned with the inward-dependency family?
2. Does the domain have meaningful cross-entity behaviour worth a separate ring?
3. Is the team's vocabulary onion (rings, Domain Services), not hexagonal (ports) or clean (Boundaries)?
4. Does the explicit Domain Services ring add clarity to this codebase?

Two yeses out of four: onion is a candidate. Compare with hexagonal and clean in `software-architecture/references/04-comparison-matrix.md` and in `references/06-relationship-with-other-architectures.md` of this skill.

If the Domain Services ring would be empty, hexagonal or clean is the lighter fit.
