# DDD Skill

Use this skill when designing, reviewing, or refactoring software using Domain-Driven Design, especially when the user mentions DDD, bounded contexts, aggregates, ubiquitous language, domain model, application services, repositories, domain events, or tactical/strategic design.

DDD is a design method. It is independent from any architecture (hexagonal, clean, onion, layered). When the user mentions hexagonal, ports and adapters, or layer placement, that is architecture and belongs in the corresponding architecture skill, not here. Finish the DDD step first; load the architecture skill afterwards.

## Non-negotiable rule

Never start with folders, layers, repositories, controllers, queues, databases, CQRS, Event Sourcing, or Event-Driven Architecture.

Always start with the domain.

The mandatory order is:

1. Determine whether DDD applies.
2. Identify business capabilities and subdomains.
3. Discover language and domain concepts.
4. Define bounded contexts.
5. Define context boundaries and ownership.
6. Create the context map.
7. Model each bounded context internally.
8. Only then discuss tactical patterns.
9. Only then discuss architecture/layout.
10. Only then discuss persistence, messaging, integration, CQRS, Event Sourcing or EDA.

## Hard rule about bounded contexts

A bounded context is not a folder, module, package, namespace, microservice, repository, database schema, queue, consumer, or API.

A bounded context is a semantic boundary where a specific model and ubiquitous language are valid.

Code structure may reflect bounded contexts, but it does not define them.

## Forbidden shortcuts

Do not say:

* "This bounded context is this folder."
* "Each aggregate gets a repository by default."
* "Each entity maps to a table."
* "Each use case requires CQRS."
* "Domain events imply Event Sourcing."
* "Hexagonal architecture means DDD is correctly applied."
* "A module/microservice is automatically a bounded context."

If the current design starts from folders, layers, or technical components, stop and reframe it from the domain.

## Mandatory review behavior

When reviewing an existing architecture, first check:

1. Are bounded contexts identified as business/model boundaries, not folders?
2. Is there one clear ubiquitous language per bounded context?
3. Are aggregates defined from invariants, not from database tables?
4. Are repositories only used where aggregate persistence is needed?
5. Are application services orchestrating use cases without containing domain rules?
6. Are domain services used only for domain logic that does not naturally belong to an entity or value object?
7. Are infrastructure concerns kept outside the domain model? (Architectural placement is the architecture skill's territory; here the question is only whether the domain stays clean.)
8. Are events used correctly: domain events as business facts, integration events as cross-boundary communication?
9. Are CQRS, Event Sourcing, or EDA introduced only when justified?

## If the user asks for architecture

Do not propose a folder structure until the strategic model is clear.

First produce:

* subdomains
* bounded contexts
* context map
* responsibilities per context
* key language per context
* aggregates and invariants per context
* use cases/application services per context

After this DDD work is complete, load the relevant architecture skill (hexagonal, clean, onion, layered, MVC) to decide layout, ports, adapters, and dependency rules. DDD does not decide architecture; architecture is a separate decision applied to the DDD output.

## Pattern boundary

CQRS, Event Sourcing and Event-Driven Architecture are not default DDD steps.

They are optional patterns that may be considered only after the domain model, bounded contexts, context map, aggregates, and use cases are understood.

Never introduce them as part of the initial DDD discovery flow.

## References

Load these when needed:

* `references/01-ddd-foundations.md` for what DDD is, when it applies, and when it does not.
* `references/02-strategic-design.md` for subdomains, business capabilities, core/supporting/generic domains, and strategic discovery.
* `references/03-bounded-contexts.md` whenever bounded contexts are being defined or reviewed.
* `references/04-context-mapping.md` for upstream/downstream relationships and context maps.
* `references/05-tactical-patterns.md` for entities, value objects, aggregates, domain services, repositories, factories and domain events.
* `references/06-application-layer.md` for use cases, application services, commands, queries, transactions and orchestration.
* `references/07-integration-patterns.md` only when discussing messaging, integration events, CQRS, Event Sourcing or EDA.
* `references/08-review-checklist.md` when reviewing an existing design or codebase.
* `references/09-user-preferences.md` for Francisco’s project conventions.

For architectural concerns (ports, adapters, layers, dependency direction, layout), do NOT load a reference here. Load the corresponding architecture skill instead (`hexagonal-architecture`, `clean-architecture`, `onion-architecture`, etc.).

Important: do not create separate skills for CQRS, Event Sourcing or Event-Driven Architecture yet. This `ddd` skill must establish the correct DDD order first.
