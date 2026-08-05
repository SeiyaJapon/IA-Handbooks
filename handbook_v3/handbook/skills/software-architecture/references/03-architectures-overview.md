# 03. Architectures Overview

This is the catalogue of architectures covered in this handbook, with a one-paragraph summary and a pointer to each specific skill. Each architecture has its own skill at the same level as the mega-skill `SKILL.md`.

The architectures are grouped by family. Within each family, picking one is choosing it as the structural decision. Across families, some compose with others.

## Family 1: Layered architectures of the inward-dependency family

These three are siblings. They share the same core principle (the dependency rule: dependencies point inward, the domain is at the centre, technologies are at the edge). They differ in vocabulary, granularity, and historical origin.

**Pick exactly one** for a given codebase. Do not mix vocabularies.

### Hexagonal Architecture

- Author: Alistair Cockburn, 2005.
- Also called: Ports and Adapters.
- Core idea: the application core defines ports (interfaces it needs or offers); adapters implement them. Two regions (inside and outside the hexagon) and three operational layers (`domain/`, `application/`, `infrastructure/`).
- When to pick: long-lived systems with rich domains where isolation from frameworks and infrastructure is valuable.
- Skill: `hexagonal-architecture/`.

### Clean Architecture

- Author: Robert C. Martin, 2012.
- Core idea: four concentric circles (Entities, Use Cases, Interface Adapters, Frameworks & Drivers) with the dependency rule pointing inward.
- When to pick: same forces as hexagonal; preferred when the team's literature is Uncle Bob's books and the four-circle vocabulary is more familiar.
- Skill: `clean-architecture/`.

### Onion Architecture

- Author: Jeffrey Palermo, 2008.
- Core idea: rings around the domain model (Domain Model, Domain Services, Application Services, External Layer) with the dependency rule pointing inward.
- When to pick: same forces as hexagonal; preferred when the project values explicit separation of domain services and application services as different rings.
- Skill: `onion-architecture/`.

## Family 2: Classical layered

A predecessor to the inward-dependency family. Recorded here because many existing systems use it and migration paths often start from it.

### Layered Architecture

- Origin: pre-1990s, dominant in early enterprise software.
- Core idea: presentation, business, data layers with downward dependencies (presentation calls business, business calls data).
- When to pick: simple to moderate domains, mid-term lifetime, framework-driven projects where the framework already imposes layering.
- When NOT to pick: long-lived rich-domain systems where the data layer leaks types upward and the business layer ends up coupled to the database.
- Skill: `layered-architecture/`.

## Family 3: UI structuring

These are not full system architectures by themselves in modern usage; they structure the presentation side. Treated as architectures in older literature when applied to whole desktop applications.

### MVC (Model-View-Controller)

- Author: Trygve Reenskaug, 1979.
- Core idea: three roles (model, view, controller) split the presentation layer's responsibilities.
- When to pick: as a structure inside the HTTP driving adapter of a hexagonal/clean codebase, or for desktop UIs with simple business logic.
- When NOT to pick: as the system-wide architecture for a long-lived rich-domain system.
- Skill: `mvc/`.

## Family 4: Deployment-defined architectures

These are architectures because they constrain the structural organisation of the system, not just operational choices. They compose with the architectures of family 1 (each unit can be hexagonal, clean, or onion internally).

### Microservices Architecture

- Origin: popularised in the 2010s.
- Core idea: each service is independently deployable, owns its data, communicates over the network with explicit contracts.
- When to pick: large systems with multiple teams, asymmetric change rates, scale that justifies the operational cost.
- When NOT to pick: small systems, single teams, when a modular monolith would do.
- Skill: `microservices-architecture/`.

### Serverless Architecture

- Origin: AWS Lambda 2014, generalised since.
- Core idea: code runs in short-lived, managed compute. The platform owns the runtime; the developer owns the function.
- When to pick: workloads with bursty or unpredictable load, when operational simplicity matters more than runtime control, when costs scale better with usage than with reservation.
- When NOT to pick: long-running processes, latency-critical paths where cold starts matter, complex local state.
- Skill: `serverless-architecture/`.

## Family 5: Communication paradigm as architecture

A paradigm at the system level that is treated as an architecture in modern usage because it constrains structure deeply.

### Event-Driven Architecture (EDA)

- Origin: 1990s in enterprise systems; popularised in modern usage with cloud and streaming platforms.
- Core idea: components communicate primarily through events. The system reacts to events more than it serves direct requests.
- When to pick: workflows that are naturally asynchronous, multiple consumers of the same event, decoupled component evolution.
- When NOT to pick: small systems where everything is synchronous, teams without broker-operations maturity.
- Skill: `event-driven-architecture/`.

## How to use this catalogue

1. Read the mega-skill `SKILL.md` first to understand what an architectural decision is.
2. Read `references/02-how-to-choose-an-architecture.md` to identify the forces in your case.
3. Use this overview to shortlist candidates.
4. Read `references/04-comparison-matrix.md` for cross-architecture comparison.
5. Read the specific sub-skill of the candidate(s) for full detail.
6. Decide. Write down the reason.

## Architectures NOT listed

Some patterns are sometimes called architectures but are not in this catalogue:

- **CQRS** is a pattern (see `architecture-patterns/cqrs-decision/`).
- **Event Sourcing** is a persistence pattern (see `architecture-patterns/event-sourcing/`).
- **DDD** is a design method (see `software-design/ddd/`).
- **Service-Oriented Architecture (SOA)** is a predecessor to microservices, not a current default. Not a separate skill in this handbook.
- **Pipe-and-Filter** is a pattern for data flow, not a system-wide architecture in the sense used here.
- **Plugin architectures** are a structural pattern; covered as part of hexagonal in this handbook (each plugin is an adapter).

If a case requires an architecture not listed here, propose adding a new sub-skill. Do not improvise vocabulary inside another architecture's skill.
