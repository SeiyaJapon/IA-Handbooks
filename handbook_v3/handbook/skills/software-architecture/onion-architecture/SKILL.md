# Onion Architecture Skill

Use this skill when designing, reviewing, or refactoring software using Onion Architecture as defined by Jeffrey Palermo (2008). Triggers: onion architecture, Palermo, rings, Domain Model ring, Domain Services ring, Application Services ring, External ring, infrastructure as the outer ring.

## What onion is

Onion Architecture was introduced by **Jeffrey Palermo** in 2008 in a series of blog posts. The name comes from the metaphor of an onion: concentric **rings** around the Domain Model at the centre, peeled outward to External code.

The four canonical rings, from inside out:

1. **Domain Model** : entities, value objects.
2. **Domain Services** : domain behaviour that does not belong to a single entity.
3. **Application Services** : use cases / orchestration.
4. **External Layer** (Infrastructure / UI) : everything outside the domain.

Onion's distinguishing contribution is the **explicit separation of Domain Services and Application Services as distinct rings**. Hexagonal collapses them into "the application core". Clean folds Domain Services into Entities or Use Cases. Onion names them as separate rings.

The non-negotiable property is **inward-only dependencies**: outer rings know inner rings; inner rings do not know outer rings.

## What onion is and is not

Onion is a **system architecture**, sibling of hexagonal and clean. The three share the inward-dependency principle but differ in vocabulary, ring partition, and what they prescribe.

Onion is NOT:

- Hexagonal. Hexagonal speaks of ports and adapters; onion speaks of rings and interfaces declared by inner rings.
- Clean. Clean has four circles with prescribed Boundaries and three named adapter roles; onion has rings without prescribing those.
- A folder template. The ring metaphor and the inward direction are what make onion onion.
- A design method. Rings are layers, not modelling instructions.
- DDD. Onion is the structure; DDD is the modelling. They compose well because onion's Domain Model and Domain Services rings align with DDD's tactical patterns.

## Vocabulary discipline

This skill uses **Palermo's vocabulary** consistently:

- **Rings**, not circles (clean) or regions (hexagonal).
- **Domain Model ring**, **Domain Services ring**, **Application Services ring**, **External Layer**.
- **Interfaces declared by inner rings** for the dependency inversion mechanism.
- **Domain Service** for cross-entity domain behaviour, as a distinct concept from Application Service.

Do not import vocabulary from hexagonal ("ports", "driving/driven adapters") or clean ("Interactors", "Boundaries", "Controllers/Presenters/Gateways") into onion discussions. The architectures are siblings; mixing vocabularies obscures which one is in force.

## Non-negotiable rule

Never start onion work from folders or frameworks.

The mandatory order is:

1. Identify the **Domain Model**: entities, value objects.
2. Identify the **Domain Services**: behaviour that crosses multiple entities and is intrinsic to the business.
3. Identify the **Application Services**: use cases that orchestrate Domain Model and Domain Services.
4. Define **interfaces** that the inner rings need from the outside.
5. Build the **External Layer**: implementations of those interfaces (persistence, transport, framework, UI).
6. Only then discuss folders, framework wiring, concrete technologies.

## Hard rule about the dependency direction

Source code dependencies point inward only:

- Domain Model depends on nothing.
- Domain Services depend on Domain Model.
- Application Services depend on Domain Services and Domain Model.
- External Layer depends on Application Services, Domain Services, and Domain Model.

Outer rings know inner rings. Inner rings never import outer ones.

Inversion happens through **interfaces declared by inner rings and implemented by outer rings**. Onion does not name them as "ports" (that is hexagonal) or "Boundaries" (that is clean); they are simply interfaces, owned by the ring that needs them.

## Hard rule about Domain Services

This is onion's distinguishing prescription. **Domain Services hold domain logic that crosses multiple entities and is intrinsic to the business**, separate from Application Services which orchestrate use cases.

A Domain Service:

- Lives in the **Domain Services ring**, between Domain Model and Application Services.
- Operates on multiple Domain Model objects.
- Speaks the ubiquitous language.
- Is stateless or holds only domain-meaningful state.
- Has no knowledge of transactions, persistence, frameworks, UI.

An Application Service:

- Lives in the **Application Services ring**, outside Domain Services.
- Orchestrates Domain Model and Domain Services to satisfy a use case.
- Knows about transactions, orchestration, dispatch of events.
- Declares interfaces for what it needs from the External Layer.

Confusing the two collapses onion into hexagonal or clean. The ring partition is what makes onion onion.

## Forbidden shortcuts

- "Onion is hexagonal with different folder names."
- "Domain Services and Application Services are the same."
- "If we have rings, we have onion."
- "Onion = DDD."
- "We do not need a Domain Services ring; let us put cross-entity logic in Application Services."

If a discussion uses hexagonal or clean vocabulary inside an onion codebase, stop and reframe in onion's vocabulary.

## Mandatory review behavior

When reviewing an onion architecture, check in order:

1. Are the four rings identifiable? (Domain Model, Domain Services, Application Services, External.)
2. Is the inward-only dependency direction respected?
3. Is the Domain Model free of framework, ORM, HTTP, SDK imports?
4. Are Domain Services genuinely cross-entity domain logic, not orchestration of use cases?
5. Are Application Services orchestrating, not containing intrinsic domain rules?
6. Is the External Layer implementing interfaces declared by inner rings?
7. Is vocabulary consistent (no "ports", "circles", "Interactors" creeping in)?
8. Is the Domain Services ring populated, or empty? If empty, is onion the right choice (hexagonal or clean may fit better)?

## When to pick onion over hexagonal or clean

Pick onion when:

- The team's vocabulary speaks rings and Palermo's literature.
- The domain has **meaningful cross-entity behaviour** that benefits from being named explicitly as Domain Services.
- The distinction between domain logic (intrinsic to the business) and application orchestration (specific to this app) is a useful axis.

Pick hexagonal when ports and adapters fit better. Pick clean when Boundaries and named adapter roles fit better.

## When NOT to pick onion

- The Domain Services ring would be empty. Without cross-entity domain logic, onion's specific contribution is absent; hexagonal or clean is a lighter fit.
- Trivial CRUD: overkill.
- Short-lived projects: overkill.
- Teams already speaking hexagonal or clean: pick one. Migrating is mostly cosmetic.

## References

- `references/01-foundations.md` : origin, when onion applies, when it does not.
- `references/02-the-four-rings.md` : Domain Model, Domain Services, Application Services, External Layer in detail.
- `references/03-the-dependency-rule.md` : inward direction, interfaces declared by inner rings.
- `references/04-domain-services-vs-application-services.md` : onion's distinguishing axis.
- `references/05-the-external-layer.md` : what lives in the External Layer; how implementations satisfy inner-ring interfaces.
- `references/06-relationship-with-other-architectures.md` : onion vs hexagonal vs clean, and why not to mix vocabulary.
- `references/07-anti-patterns.md` : recurring failures in onion codebases.
- `references/08-review-checklist.md` : operational checklist.
- `references/09-user-preferences.md` : Francisco's project conventions.

Important: do not load hexagonal or clean skills as part of onion work. They are siblings with different vocabularies. If the case crosses into one, finish the onion step first.
