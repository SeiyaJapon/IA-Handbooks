# 07. Relationship with Other Disciplines

Hexagonal Architecture is often confused with neighbouring disciplines: other architectures, design methods, patterns, paradigms. This document draws the lines.

The skill `hexagonal-architecture` covers hexagonal only. The other concepts have their own skills. When a discussion crosses into one of them, finish the hexagonal step first, then load the appropriate skill.

The disciplines below are split into two groups:

- **Architectures** (siblings or alternatives to hexagonal): different ways to structure a system. Choosing one is choosing a substitute for hexagonal in that role.
- **Design methods, patterns, and paradigms** (orthogonal to hexagonal): different kinds of decisions. They can coexist with hexagonal or with any other architecture.

---

## Architectures

These are the disciplines that compete with or sit next to hexagonal in the same role. Picking one is picking the structure of the system.

### Hexagonal vs Clean Architecture

Clean Architecture (Robert C. Martin, 2012) shares hexagonal's central principle: the dependency rule. It expresses it differently.

What is shared:

- The domain is at the centre, framework-free.
- External technologies are at the edge.
- Inversion of control via interfaces (ports in hexagonal, abstract use case interfaces in clean).
- The dependency rule as the non-negotiable property.

What is different:

- Clean Architecture defines **four concentric circles**: Entities, Use Cases, Interface Adapters, Frameworks & Drivers.
- Hexagonal defines **two regions**: inside the hexagon (the application) and outside (the actors and adapters), with three layers in the typical operational expression (`domain/`, `application/`, `infrastructure/`).
- Clean Architecture is more prescriptive about the role of each circle (Entities = enterprise rules, Use Cases = application rules).
- Hexagonal is more agnostic: ports and adapters do not prescribe a specific shape inside the hexagon.

Practical guidance:

- They are siblings. A team that works in either is not far from the other.
- Choose one vocabulary for the project and stay there. Mixing "ports / adapters" with "interface adapters / frameworks & drivers" in the same conversation is confusing without benefit.
- This handbook treats them as separate skills with separate vocabulary. Use the `clean-architecture` skill when discussing clean; do not import its vocabulary into a hexagonal discussion.

### Hexagonal vs Onion Architecture

Onion Architecture (Jeffrey Palermo, 2008) is another sibling. Same principle, slightly different vocabulary.

What is shared:

- Concentric layers with the domain at the centre.
- The dependency rule.
- Interfaces (ports) defined in the inner layers, implemented in the outer.

What is different:

- Onion uses the metaphor of rings (Domain Model, Domain Services, Application Services, External Layer).
- Onion places domain services and application services as separate rings; hexagonal collapses them into the application core.
- Onion's vocabulary is more granular about the kinds of services in the core.

Practical guidance:

- Same as Clean Architecture: a sibling, not a duplicate. Pick one vocabulary, stay there.
- The `onion-architecture` skill handles onion. Do not import its vocabulary here.

### Hexagonal vs Layered Architecture (presentation / business / data)

Classic layered architecture is what hexagonal was reacting against.

- Layered: dependencies typically point downward (presentation → business → data), and the data layer leaks types upward.
- Hexagonal: dependencies point inward, and the data layer is just one of many adapters around the core.

A "layered" architecture can be made into a hexagonal one by:

1. Renaming `presentation/` to driving adapters in `infrastructure/`.
2. Inverting the data layer so the persistence interfaces live in the core, not in the data layer.
3. Identifying the core as the union of the application and domain layers.

These are not cosmetic renames; the dependency direction is what changes. The `layered-architecture` skill (when written) covers classical layered as its own structure.

### Hexagonal vs MVC

MVC (Model-View-Controller) is a UI structure for the presentation side, often classified as an architecture in older literature when it was applied to whole desktop applications. Today it most often lives **inside** a driving adapter.

- MVC organises the presentation layer (or a UI module) into model, view, and controller pieces.
- Hexagonal organises the entire application around its core.

In a hexagonal application, MVC may live inside one of the driving adapters (the HTTP adapter), structuring how requests are handled. The model in MVC is a view-side model (a DTO, a presenter); it is not the domain model of hexagonal.

Confusing the two leads to placing business logic in MVC controllers, which violates hexagonal's dependency rule. The `mvc` skill (when written) covers MVC as its own concern.

### Hexagonal vs Microservices

Microservices is an **architecture and deployment pattern**: each service is independently deployable.

- A microservice can be hexagonal (the recommended shape).
- A monolith can be hexagonal (the recommended shape if the monolith is going to outlive its current stack).
- Microservices is not a substitute for hexagonal at the level of a single service; it is a separate decision about how the whole system is shipped.

A team that says "we are doing microservices, so we don't need hexagonal" has confused two layers of decision. Each service still benefits from the hexagonal boundaries internally. The `microservices-architecture` skill covers microservices as its own discipline.

### Hexagonal vs Serverless

Serverless is an **architecture and runtime model**: code runs in short-lived, managed compute (Lambda, Cloud Functions).

- A serverless function can be the entry point of a hexagonal application: the function handler is a driving adapter.
- Serverless does not change the hexagonal core; it changes how the driving adapter is dispatched.

The `serverless-architecture` skill covers serverless-specific concerns.

### Hexagonal vs Event-Driven Architecture (EDA)

Event-Driven Architecture is a **paradigm** that organises the system around events as the primary mode of communication. In modern usage it is treated as an architecture in its own right.

- A hexagonal codebase can be event-driven: events flow through driven adapters (publishers) and driving adapters (consumers).
- An event-driven system can also exist without hexagonal: a system that emits events from procedural code with no isolated core is event-driven but not hexagonal.

In a hexagonal codebase that is event-driven:

- The event publisher is a driven adapter implementing an event-bus port.
- The event consumer is a driving adapter that translates the incoming event into a call on a driving port.

Hexagonal and EDA compose. The `event-driven-architecture` skill covers EDA as its own discipline.

---

## Design methods, patterns, and paradigms (orthogonal to hexagonal)

These disciplines are NOT alternatives to hexagonal. They are different decisions on different axes. Hexagonal can coexist with any of them, none of them, or only some of them.

### Hexagonal and Domain-Driven Design (DDD)

Different problems, different decades, different authors.

- **Hexagonal** (Alistair Cockburn, 2005) is an **architecture** that isolates the application core from the technologies that surround it.
- **DDD** (Eric Evans, 2003) is a **design method** for the inside of the core, focused on modelling the domain in code with the language of the business.

What they share:

- Both push the domain to the centre.
- Both insist that frameworks and persistence are outside concerns.

What they do not share:

- Hexagonal says nothing about how to model entities, value objects, aggregates, or domain events. That is DDD.
- DDD says nothing about ports and adapters as a structural concept. That is hexagonal.

How they compose:

- A hexagonal architecture is the natural home for a DDD core. The hexagon's interior is where DDD's tactical patterns live.
- A hexagonal architecture can have a non-DDD core (transaction script, active record, simple procedural logic).
- A DDD core can run inside a non-hexagonal architecture (a layered framework-coupled stack), although the boundaries are weaker.

If the case requires both, do them in order: define hexagonal boundaries first (ports, adapters, layers, dependency rule), then model the inside with DDD. Loading both skills is fine; mixing their vocabularies in one document is not. This skill stops at the boundary; what is inside the core is the `ddd` skill's territory.

### Hexagonal and CQRS

CQRS (Command Query Responsibility Segregation) is a **pattern**, not an architecture.

- CQRS separates write operations (commands) from read operations (queries) into distinct models.
- It can be applied inside a hexagonal architecture, inside a clean architecture, inside a layered architecture, or inside no architecture at all.

In a hexagonal codebase that uses CQRS:

- Commands and queries are data carriers in `application/`.
- Command handlers and query handlers are driving ports.
- Adapters call those handlers via the ports.

CQRS does not change the hexagonal boundaries. It refines the shape of the driving ports. The `cqrs-decision` skill covers when CQRS is justified.

### Hexagonal and Event Sourcing

Event Sourcing is a **persistence pattern**: the source of truth is the sequence of events that produced the state, not the state itself.

- Event Sourcing changes how repositories work (they replay events instead of loading rows).
- It changes how aggregates are reconstructed.
- It does not change the hexagonal boundaries: the repository is still a driven port, the event store is the underlying technology, the application service still orchestrates without knowing how persistence works.

Event Sourcing inside hexagonal is fine. Event Sourcing outside hexagonal is fine. Neither requires the other. The `event-sourcing` skill covers it.

---

## Summary

Hexagonal is one decision. The others are separate decisions on different axes:

| Discipline | Type | Independent of hexagonal? |
|---|---|---|
| Clean Architecture | Architecture (sibling) | No: choose one or the other |
| Onion Architecture | Architecture (sibling) | No: choose one or the other |
| Layered Architecture | Architecture (predecessor) | No: choose one or the other |
| MVC | UI structure / architecture | Composable inside a driving adapter |
| Microservices | Architecture and deployment | Composable; per-service still benefits from hexagonal |
| Serverless | Architecture and runtime | Composable; the function handler is a driving adapter |
| Event-Driven Architecture | Paradigm and architecture | Composable; EDA can use hexagonal internally |
| DDD | Design method | Yes, fully orthogonal |
| CQRS | Pattern | Yes, fully orthogonal |
| Event Sourcing | Persistence pattern | Yes, fully orthogonal |

When two of these are in the conversation, treat each as its own decision. Do not collapse them into one discussion, and do not assume that one implies the others.
