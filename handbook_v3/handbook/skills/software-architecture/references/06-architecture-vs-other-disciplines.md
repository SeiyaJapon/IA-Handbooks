# 06. Architecture vs Other Disciplines

Architecture is constantly conflated with neighbouring disciplines. This document draws the lines and explains how each composes (or does not compose) with architectural decisions.

This is the cross-discipline view. For architecture-vs-architecture comparison, see `07-relationship-with-other-disciplines.md` of each specific architecture skill.

## Architecture vs design method

**Architecture** decides the structure of the system: which parts exist, how they relate, where dependencies go.

**Design methods** decide how the inside of the core is modelled: how entities, value objects, aggregates, services are constructed.

Examples of design methods:

- Domain-Driven Design (DDD).
- Transaction script.
- Active record.
- Table module.
- Service layer.

How they compose with architecture:

- A hexagonal architecture can host a DDD core, a transaction-script core, an active-record core, etc.
- A layered architecture can host any of them, with weaker boundaries.
- A microservices architecture has one design method per service, possibly different ones in different services.

Common error: "we are doing DDD" answered as "what is the architecture?". DDD is a design method; the architecture is a separate decision. A DDD core in a layered framework-coupled stack is DDD, but the architecture is layered, not "DDD".

Skill: `software-design/ddd/`. Other design methods do not have dedicated skills in this handbook yet.

## Architecture vs pattern

**Architecture** is the structural skeleton.

**Patterns** are reusable techniques applied within an architecture.

Examples of patterns:

- CQRS (Command Query Responsibility Segregation).
- Event Sourcing.
- Repository.
- Outbox.
- Saga.
- Anti-corruption layer.
- Strategy, Factory, Observer, and other GoF patterns.

How they compose with architecture:

- CQRS shapes the driving ports. Compatible with hexagonal, clean, onion, layered.
- Event Sourcing shapes the driven repository. Compatible with all the above.
- Repository is itself an architectural-adjacent pattern; it shows up in most architectures with similar meaning.
- GoF patterns operate at code level, below the architectural level.

Common error: "the architecture is CQRS". CQRS is a pattern; the architecture is whatever structure hosts it. CQRS without an architecture is just a code-level separation; CQRS with hexagonal is a pattern applied inside a structural skeleton.

Skills: `architecture-patterns/cqrs-decision/`, `architecture-patterns/event-sourcing/`. Other patterns may live in `software-design/`.

## Architecture vs paradigm

**Architecture** is the structural skeleton.

**Paradigms** are language-level approaches to expressing logic.

Examples of paradigms:

- Object-Oriented (OO).
- Functional.
- Procedural.
- Logic.
- Declarative.

How they compose with architecture:

- A hexagonal codebase can be OO, functional, or procedural inside the core.
- A microservices codebase can have services in different paradigms (and different languages).
- The paradigm shapes how code is written; the architecture shapes how it is organised.

Common error: "we are doing functional, so we don't need an architecture". The paradigm constrains code style; the architecture constrains structure. Both are needed.

Note: Event-Driven Architecture (EDA) is sometimes called a paradigm, sometimes an architecture. In this handbook it is treated as an architecture (see `architectures-overview.md`).

## Architecture vs framework

**Architecture** is the structural skeleton, independent of any framework.

**Frameworks** (NestJS, Spring, Laravel, Django, Express, Rails) impose conventions, provide infrastructure (DI, routing, ORM integration), and shape how the code is wired.

How they compose:

- The same architecture can be expressed with several frameworks.
- The same framework can host several architectures.
- The framework lives in `infrastructure/` (in inward-dependency architectures). It does not define the architecture.

Common error: "the architecture is NestJS" or "we picked NestJS, so we are using its architecture". NestJS is a framework. The architecture is a separate decision.

When a framework imposes a default folder structure (like NestJS modules in `src/<feature>/`), that default is **not** an architecture. Adopting the default is acceptable, but it is the team's choice; the architecture is the dependency rules they enforce on top.

Frameworks do not have skills in this handbook unless their conventions become architectural (none currently).

## Architecture vs deployment

**Architecture** is the structural skeleton.

**Deployment** is how the system is shipped: monolith, microservices, serverless, containers, lambdas, on-prem, cloud, multi-region, etc.

The line is partial:

- "We use Kubernetes" is operational, not architectural.
- "We use AWS Lambda" is operational, but if the architecture is built around serverless functions, that is also architectural.
- "Each service is independently deployable" is architectural because it forces structural boundaries.
- "We deploy with Terraform" is operational.

Microservices and serverless are architectures because they force structural decisions. Generic deployment platforms are not architectures; they support the architecture chosen.

Common error: "we deploy with Lambda, so the architecture is serverless". Lambda is the platform; serverless can be the architecture if the system is structured around functions. If the system is a single Lambda that internally runs a hexagonal application, the architecture is hexagonal hosted on serverless infrastructure.

Skills: `software-architecture/microservices-architecture/`, `software-architecture/serverless-architecture/`, plus auxiliary skills like `aws-infrastructure/`.

## Architecture vs SOLID and code quality

**Architecture** decides structure.

**SOLID and code quality principles** (clean code, naming, function length, error handling) decide how individual files and classes are written.

How they compose:

- A hexagonal codebase can have SOLID-respecting code or SOLID-violating code; the architecture is independent of code-level quality.
- SOLID-respecting code in a layered architecture does not turn it into hexagonal.
- Architecture and code quality both matter; neither replaces the other.

Common error: "the architecture is SOLID" or "if the code is clean, the architecture is fine". They are separate.

Skills: `software-design/solid-principles/`, `software-design/design-patterns/`, `code-readability/`, `software-design/error-handling-patterns/`.

## Architecture vs operational concerns

**Architecture** is the structural skeleton.

**Operational concerns** are observability, security, performance, compliance, deployment automation, on-call.

How they compose:

- The architecture must support the operational requirements (auditability, isolation, fault tolerance), but the architecture is not the operational implementation.
- "We need observability" is not an architectural decision unless it constrains structure (e.g. "every service emits structured logs in format X" is operational, "every command emits a domain event for audit" is architectural).

Skills: `observability/`, `security-review/`, `performance-analysis/`, `software-architecture/compliance-patterns/`.

## Quick reference

| Discipline | Decides | Skill location |
|---|---|---|
| Architecture | Structure of the system | `software-architecture/` (this skill) |
| Design method | How the core is modelled | `software-design/` (DDD lives here) |
| Pattern | Technique applied within an architecture | `architecture-patterns/` and `software-design/` |
| Paradigm | Code-level approach (OO, functional, etc.) | not currently a separate skill |
| Framework | Code wiring conventions | not a skill (lives in `infrastructure/` of the codebase) |
| Deployment | How the system is shipped | partial overlap with architecture (microservices, serverless) |
| SOLID / code quality | How individual code is written | `software-design/solid-principles/`, `code-readability/` |
| Operational | Observability, security, performance | dedicated skills (`observability/`, `security-review/`, etc.) |

## Output

When this document is applied, the team can answer:

- For each decision on the table, which discipline does it belong to?
- Which skill should be loaded to discuss it?
- Are decisions of different disciplines being conflated in the conversation?

Without that classification, conversations slide between disciplines and conclude with answers that solve a different question than the one asked.
