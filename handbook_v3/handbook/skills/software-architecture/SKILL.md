# Software Architecture Skill

Use this skill when discussing, designing, choosing, comparing, reviewing, or migrating software architectures. Architecture decides the **structure** of a system: which parts exist, how they relate, where dependencies go, what is allowed to change without breaking what.

This skill is the entry point for everything architectural. It does not replace specific architecture skills (`hexagonal-architecture`, `clean-architecture`, `onion-architecture`, `layered-architecture`, `mvc`, `microservices-architecture`, `serverless-architecture`, `event-driven-architecture`, etc.). It governs how those skills are reached and used together.

## What this skill is for

- Decide whether the case is architectural at all (vs design, vs patterns, vs paradigms).
- Pick which architecture (or which combination) fits the problem.
- Compare architectures, including ones the team is not currently using.
- Plan migrations between architectures.
- Review an existing architecture as a whole, before drilling into its specific style.

When the case is clearly inside one architecture (e.g. "is this hexagonal correct?"), load the specific architecture skill directly. This skill is for the cross-architecture decisions.

## Non-negotiable rule

Never start architectural work from folders, frameworks, transports, or persistence.

Always start from the **shape of the problem**: what the system does, what it must isolate from change, what teams own what, what is the expected lifetime, what scale is in scope.

The mandatory order is:

1. State what kind of decision is on the table (architecture vs design vs pattern vs paradigm). Architecture is the **structure of the system**; the others compose with it.
2. Identify the forces that should drive the architecture: domain complexity, team size and topology, expected lifetime of the code, change rate, scale, regulatory or operational constraints.
3. Identify candidate architectures (one or more). Do not rule out alternatives without evaluating.
4. For each candidate, identify the cost (ceremony, learning curve, operational overhead) and the benefit (isolation, longevity, testability, scaling).
5. Pick one architecture as the structural decision. Composable architectures (microservices, serverless, EDA) may compose on top.
6. Load the specific architecture skill and apply it.
7. Only after the architecture is decided, discuss design (DDD, modelling), patterns (CQRS, ES, SOLID, GoF), and paradigms (functional, OO).

## Hard rule about what an architecture is

A software architecture is a **structural decision**: it constrains how the parts of a system are organised, how they depend on each other, and what is allowed to change.

A software architecture is NOT:

- A folder layout. Folders may reflect the architecture but do not define it.
- A framework. A framework may impose conventions, but architecture is independent of any framework.
- A design method. DDD, transaction script, active record are design methods, not architectures.
- A pattern. CQRS, Event Sourcing, Repository, Outbox are patterns; they apply on top of an architecture.
- A paradigm. Functional, OO, procedural are paradigms; they constrain code style, not system structure.
- A deployment strategy by itself. Microservices and serverless are architectures because they decide structure, but generic "we use Kubernetes" is not.

Code structure may reflect an architecture, but it does not define it. Two projects with identical folders can have completely different architectures depending on the dependency rules they enforce.

## Forbidden shortcuts

Do not say:

- "We have layers, so we have an architecture."
- "We use NestJS, so the architecture is NestJS."
- "We do DDD, so we don't need to choose an architecture."
- "Microservices is the modern architecture."
- "Hexagonal and Clean Architecture are the same."
- "We need an architecture, so we'll add CQRS and Event Sourcing."
- "The architecture is whatever the senior dev decides on the day."

If a discussion starts from folders, frameworks, or "what's modern", stop and reframe it from the forces driving the system.

## Mandatory review behavior

When reviewing an existing architecture, check in order:

1. Is there an architecture, or is the structure accidental?
2. What architecture(s) does the codebase actually express, regardless of what the team calls it?
3. Are the dependency rules of that architecture enforced (review, tooling, tests), or only documented?
4. Is the architecture appropriate for the forces of the system (domain complexity, team, lifetime, scale)?
5. Are sibling architectures mixed unintentionally (e.g. hexagonal layers next to MVC controllers next to microservice fragments)?
6. Are design methods, patterns, and paradigms confused with the architecture (e.g. "we are doing DDD" used as the architectural answer)?
7. If the architecture is wrong for the forces, is there a migration plan? Is migration planned in steps that keep the system shippable?

## If the user asks for "the architecture of this project"

Do not propose folders, frameworks, or ports until the structural decision is clear.

First produce:

- A description of the system's forces (what it does, what isolates it from change, who owns what, expected lifetime, scale).
- A shortlist of candidate architectures with their cost/benefit for these forces.
- A picked architecture (or a composition), with the reason written down.
- The specific architecture skill(s) to load next.
- A note on what is design vs pattern vs paradigm vs architecture in this case, so the team does not conflate them.

## Pattern boundary

This skill governs **architectures**. It does not govern:

- Design methods (DDD): load the `ddd` skill.
- Tactical patterns (CQRS, Event Sourcing, Repository, Outbox, Saga): load the `architecture-patterns` skills (or the relevant skill in `software-design`).
- SOLID, GoF, dependency injection: load the corresponding `software-design` skills.
- Specific runtime technologies (AWS infrastructure, Terraform): load the relevant `software-architecture` auxiliary skills or the technology's own skill.

When in doubt, classify the question first ("is this an architectural decision?"). If yes, this skill applies. If no, route it.

## References

Load these when needed:

- `references/01-what-is-software-architecture.md` for what architecture is, what it is not, and how it differs from design, patterns, paradigms, frameworks, and deployment.
- `references/02-how-to-choose-an-architecture.md` for the forces that drive architectural choice and the decision process.
- `references/03-architectures-overview.md` for the catalogue of architectures covered in this handbook, with a one-paragraph summary and a pointer to each specific skill.
- `references/04-comparison-matrix.md` for cross-architecture comparison: when each one fits, when it does not, what it composes with.
- `references/05-migration-paths.md` for how to move from one architecture to another (layered → hexagonal, monolith → microservices, etc.) without losing the system on the way.
- `references/06-architecture-vs-other-disciplines.md` for how architecture relates to design (DDD), patterns (CQRS, ES), paradigms (EDA, functional), frameworks, and deployment.
- `references/07-anti-patterns.md` for the cross-architecture failures: cargo cult, mixed axes, design-disguised-as-architecture, framework-as-architecture, etc.
- `references/08-review-checklist.md` when reviewing an existing architecture as a whole.
- `references/09-user-preferences.md` for Francisco's project conventions for architectural work.

## Sub-skills (specific architectures)

Each specific architecture has its own skill at the same level as this `SKILL.md`:

- `hexagonal-architecture/` (Cockburn 2005): isolate the core via ports and adapters.
- `clean-architecture/` (Martin 2012): concentric layers with the dependency rule.
- `onion-architecture/` (Palermo 2008): rings of services around the domain model.
- `layered-architecture/` (classical): presentation/business/data with downward dependencies.
- `mvc/` (Reenskaug 1979): UI structure with model, view, controller.
- `microservices-architecture/`: independent deployable services.
- `serverless-architecture/`: managed short-lived compute.
- `event-driven-architecture/`: events as the primary mode of communication.
- `frontend-architecture/`: SPA structure, feature modules, state, framework-shaped frontends.
- `long-running-services-architecture/`: containers, workers, persistent processes.
- `data-pipeline-architecture/`: ETL, batch, streaming, ingestion.

Each sub-skill is **complete on its own**: it has its own SKILL.md, its own references, its own review checklist. Do not load this mega-skill to drill into one specific architecture; go directly to the sub-skill.

Use this mega-skill when the question is **about choosing, comparing, migrating, or reviewing across architectures**.

Important: do not invent new architectures. If a case requires something none of the sub-skills cover, propose adding a new sub-skill explicitly; do not improvise vocabulary inside another architecture's skill.
