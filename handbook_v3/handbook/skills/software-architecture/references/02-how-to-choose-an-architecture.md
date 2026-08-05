# 02. How to Choose an Architecture

Choosing an architecture is choosing **which structural rules the system will enforce**. The choice is driven by forces (the properties of the problem and the team), not by trends, by what the senior dev knows, or by what looks modern.

This document describes the forces, the decision process, and the most common composition decisions (when one architecture is not enough).

## The forces

A small set of forces drives architectural choice. Identify them before evaluating any architecture.

### 1. Domain complexity

How rich is the business behaviour the system implements?

- **Trivial.** Forms over tables. Validation, no business rules. Few invariants. CRUD-style.
- **Moderate.** Some rules, some derived values, some lifecycle. The team can keep it in their heads.
- **Rich.** Multiple invariants per entity, lifecycle that matters, vocabulary owned by the business, decisions that depend on history.

Higher domain complexity favours architectures that isolate the domain (hexagonal, clean, onion). Trivial domains do not repay the ceremony.

### 2. Expected lifetime

How long is the code expected to live?

- **Throwaway.** Migration script, one-off batch, proof of concept, hackathon entry.
- **Mid-term.** A few months to a couple of years. Likely to evolve, not to be rewritten.
- **Long-term.** Multiple years. Will outlive its current framework, ORM, transport, and probably some of the team.

Longer lifetime favours architectures that isolate the system from the technologies it currently uses, because those technologies will change.

### 3. Team size and topology

How many people work on this system, organised how?

- **One person, one stream.** Architecture overhead is pure cost.
- **Small team, single context.** Light architecture is enough.
- **Multiple teams, multiple contexts.** Architecture must support boundaries that match team boundaries (Conway's Law).
- **Many teams, many contexts, independent release cadence.** Microservices and similar deployment-architectures become candidates because the architecture must support independent deployment.

### 4. Change rate

How fast does the system change?

- **Slow.** Months between deploys. Few features per quarter.
- **Steady.** Continuous deployment, regular feature flow.
- **Asymmetric.** Some parts change weekly, others stay still for years. Coupling them slows the fast parts to the speed of the slow.

Asymmetric change rate favours architectures that decouple change frequencies (separate contexts, separate services).

### 5. Scale

How big is the system in load, data, and concurrency?

- **Small.** A few thousand users, simple queries.
- **Medium.** Tens or hundreds of thousands of users, multiple data stores, batch jobs.
- **Large.** Millions of users, multi-region, write throughput as a first-order concern.

Scale alone does not pick an architecture, but it can disqualify some (a single-process monolith may not fit large scale; microservices may not be necessary at small scale).

### 6. Operational and regulatory constraints

What constraints come from outside the code?

- **Audit trail required.** Pushes towards event sourcing or event logging at minimum.
- **Data residency.** Pushes towards architectures that allow geographic separation.
- **High availability.** Pushes towards architectures that tolerate partial failure.
- **Compliance (PCI, HIPAA, GDPR).** Pushes towards architectures that isolate sensitive data clearly.

These constraints often dominate when present. They are non-negotiable.

### 7. Existing system

If the system already exists, what does it look like?

- **Greenfield.** Free to choose.
- **Brownfield with sound architecture.** Work within it.
- **Brownfield with broken architecture.** Choose the migration path, not the destination architecture in isolation.

A greenfield project can pick any architecture. A brownfield project must consider what is migratable.

## The decision process

A practical sequence:

1. **List the forces.** Write them down for the system in question. Be specific (not "complex domain" but "rich invariants in scenarios, simple CRUD in user profiles").
2. **Rule out incompatible architectures.** Some architectures fail forces unambiguously (microservices for a one-person hackathon project; classical layered for a long-lived rich domain).
3. **Shortlist candidates.** Two or three architectures that fit. Include their cost and benefit.
4. **Evaluate composition.** Some decisions compose: hexagonal + microservices + EDA can all coexist; that is one architectural choice with three components.
5. **Pick.** Write the decision down with the reason. The reason is the part that matters; the architecture name is shorthand.
6. **Plan enforcement.** Code review, lint rules, tests, dependency graph tools. An architecture that is not enforced is not an architecture.
7. **Plan revisit.** When do we re-evaluate? After the first major release? When the team grows past N? When the system reaches X scale?

## Common composition decisions

Architectures often compose. Knowing which compose well prevents fake conflicts.

### Hexagonal + DDD

Hexagonal isolates the core; DDD models the inside of the core. Natural fit. The most common composition for rich-domain, long-lifetime systems.

### Hexagonal + microservices

Each microservice has a hexagonal architecture internally. The microservice boundary is independent of the hexagonal boundary. Recommended when both apply.

### Hexagonal + EDA

Events flow through driven adapters (publishers) and driving adapters (consumers). Hexagonal does not change because EDA exists; EDA changes how some adapters work.

### Hexagonal + serverless

The serverless function handler is a driving adapter. The hexagonal core lives inside the deployment unit. Common for lightweight services.

### Microservices + EDA

Independent services that communicate primarily via events. Common in large systems. Each service can still be hexagonal internally.

### MVC inside a driving adapter

When the system is hexagonal, MVC may live inside the HTTP driving adapter, structuring how requests are handled. The MVC model is a view-model, not the domain model.

### Layered + DDD (limited)

A layered architecture can host a DDD core, but the boundaries are weaker than in hexagonal/clean/onion. Acceptable when the domain is moderately rich and the lifetime is mid-term, but most projects with rich domains migrate to hexagonal or clean over time.

## Common conflicts

These compositions look attractive but conflict. Recognise them.

### "Microservices because we are doing DDD"

DDD pushes for bounded contexts; microservices is a deployment strategy. They are different decisions. Bounded contexts can be implemented as microservices, modules in a monolith, or anything in between. Picking microservices because DDD does not justify the operational cost of microservices.

### "Hexagonal and Clean Architecture together"

They are siblings, not complementary. Pick one vocabulary and stay there. Mixing produces confusion without added value.

### "MVC + hexagonal as architecture"

MVC, in modern hexagonal codebases, lives inside the HTTP driving adapter. Treating MVC as the architecture and adding hexagonal on top is two architectural axes competing.

### "Microservices with shared database"

The shared database couples the services at the lowest level. The result is microservices in name, monolith in practice, with all the operational cost of microservices and none of the independence.

## Anti-patterns in architectural choice

- **Cargo cult.** "Big company X does microservices, so we should too." The forces of a big company do not match yours.
- **Resume-driven decisions.** "I want experience with EDA, so let's do EDA." This produces architectures that match the developer's career, not the project.
- **Framework first.** "We use NestJS, so the architecture is NestJS-shaped." The framework should fit the architecture, not define it.
- **Trends over forces.** "Microservices are modern, monoliths are legacy." Not true for many systems.
- **No decision.** Architecture happens by accident. The structure is whatever the first contributor wrote on the first day.
- **Over-architecting.** Pre-emptively adopting hexagonal, clean, microservices, EDA, CQRS, and ES for a CRUD admin. Cost without benefit.
- **Under-architecting.** Letting a long-lived, rich-domain, multi-team system run on layered architecture with no enforced boundaries. Cost compounds over years.

## Output

When this document is applied, the team can produce:

- A list of forces, written down for the system in question.
- A shortlist of candidate architectures with cost/benefit.
- A picked architecture (or composition) with the reason.
- A plan for enforcement and a date for revisit.

Without these, the architecture is implicit and accidental.
