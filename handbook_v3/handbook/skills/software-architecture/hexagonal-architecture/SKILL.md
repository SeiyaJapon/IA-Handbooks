# Hexagonal Architecture Skill

Use this skill when designing, reviewing, or refactoring software using Hexagonal Architecture (also known as Ports and Adapters), especially when the user mentions hexagonal, ports, adapters, driving/driven adapters, application core, layered direction, dependency rule, or isolating the domain from infrastructure.

## What hexagonal is

Hexagonal Architecture was introduced by Alistair Cockburn in 2005. Its purpose is to **isolate the application core from the technologies that surround it** (frameworks, databases, transports, external systems) so the core can be developed, tested, and evolved independently of those technologies.

Hexagonal is an **architecture**, not a design method. It tells you how the system is structured. It does not tell you how to model the domain inside the core. Modelling is a separate discipline (DDD, transaction script, active record, etc.).

## What hexagonal is not

- Not a synonym of DDD. DDD (Evans, 2003) is a design method. Hexagonal is an architecture. They compose well but are independent.
- Not a synonym of Clean Architecture or Onion Architecture. Those are sibling architectures with different vocabulary; load their own skills when discussing them.
- Not a folder template. The folder layout is a consequence of the architecture, not its source.
- Not CQRS, not Event-Driven Architecture, not Event Sourcing. Those are orthogonal patterns or paradigms that may or may not coexist with hexagonal.

## Non-negotiable rule

Never start hexagonal work from folders, frameworks, transports, or persistence.

Always start from the application core: what does the system do, what does it need from the outside (driven ports), what does the outside need to ask the system (driving ports).

The mandatory order is:

1. Identify the application core (what the system does, in domain terms).
2. Identify driving ports (what the core offers to external actors).
3. Identify driven ports (what the core needs from the outside).
4. Decide adapters per port (driving and driven).
5. Decide the dependency direction (always inward).
6. Decide the layout (consequence of the above).
7. Only then discuss frameworks, ORMs, transports, and concrete technologies.

## Hard rule about layers

The valid layers are exactly three:

- `domain/`
- `application/`
- `infrastructure/`

`presentation/` is NOT a layer. HTTP controllers, GraphQL resolvers, CLI handlers, queue consumers, and any other delivery mechanism are **driving adapters**, which live in `infrastructure/`. Treat any `presentation/` folder as a finding to fix.

## Hard rule about dependency direction

Dependencies point **inward only**.

- `infrastructure/` depends on `application/` and `domain/`.
- `application/` depends on `domain/` and on port interfaces.
- `domain/` depends on nothing.

Any inversion of this rule (the domain importing infrastructure, the application importing concrete adapters) breaks the architecture. There are no exceptions for convenience.

## Forbidden shortcuts

Do not say:

- "Hexagonal means having `domain/`, `application/`, `infrastructure/` folders."
- "If we have ports and adapters, we are doing hexagonal."
- "The framework module is part of the application layer because it wires use cases."
- "The repository can import the ORM type because it lives in infrastructure anyway."
- "Hexagonal is just DDD without the modelling part."
- "Adding a `presentation/` layer makes the structure cleaner."

If the current design starts from folders, frameworks, or transports, stop and reframe it from the core.

## Mandatory review behavior

When reviewing an existing hexagonal architecture, check in order:

1. Is the application core identified, and is it framework-agnostic?
2. Are ports defined as interfaces in `domain/` or `application/` (never in `infrastructure/`)?
3. Do driven adapters in `infrastructure/` implement those ports?
4. Are driving adapters in `infrastructure/` (HTTP, CLI, queue consumers) thin and free of business rules?
5. Do dependencies point inward only, with no domain or application imports of infrastructure?
6. Are there only three layers (`domain/`, `application/`, `infrastructure/`), with no `presentation/`?
7. Is the layout (per bounded context, per layer, per feature) consistent and not mixed?
8. Are framework modules confined to `infrastructure/` and not sitting next to the layer folders?

## If the user asks for architecture

Do not propose a folder structure until the core is defined.

First produce:

- The application core in domain terms (what it does).
- Driving ports (use case interfaces).
- Driven ports (repository, external service, time, identity, notification, event bus, etc.).
- Driving adapters per driving port.
- Driven adapters per driven port.
- Dependency direction confirmed.
- Layout (consequence of the above).

## Pattern boundary

Hexagonal does not require CQRS, Event Sourcing, or any messaging pattern. It does not require DDD. It is compatible with all of them, but does not depend on any.

Never introduce CQRS, Event Sourcing, EDA, or DDD as part of the initial hexagonal flow. Those are separate decisions covered by their own skills.

## References

Load these when needed:

- `references/01-foundations.md` for what hexagonal is, who proposed it, when it applies, and when it does not.
- `references/02-ports-and-adapters.md` for ports (driving and driven) and adapters (driving and driven), with definitions and placement rules.
- `references/03-layers-and-direction.md` for the three valid layers (`domain/`, `application/`, `infrastructure/`), what lives in each, and the dependency rule.
- `references/04-driving-adapters.md` for HTTP controllers, CLI handlers, queue consumers, schedulers, message handlers, and how they translate external input into calls on driving ports.
- `references/05-driven-adapters.md` for repositories, external service clients, event bus implementations, file storage, time and identity providers, and how they implement driven ports.
- `references/06-layout-and-multiple-contexts.md` for folder layout, the rule for projects that mix hexagonal layers with framework scaffold, and how hexagonal applies when several bounded contexts coexist.
- `references/07-relationship-with-other-disciplines.md` for how hexagonal differs from sibling architectures (Clean, Onion, Layered, MVC, Microservices, Serverless, EDA) and from orthogonal disciplines (DDD, CQRS, Event Sourcing); what to load when those topics come up.
- `references/08-anti-patterns.md` for the recurring failures (presentation layer, fat domain, framework leaks, layered template without dependency rule).
- `references/09-review-checklist.md` when reviewing an existing design or codebase.
- `references/10-user-preferences.md` for Francisco's project conventions for hexagonal.

Important: do not load the DDD, Clean Architecture, or Onion Architecture skills as part of hexagonal work. They are separate skills with separate vocabulary. If the case crosses into those, finish the hexagonal step first, then load the appropriate skill.
