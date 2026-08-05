# 01. Foundations

## Origin

Hexagonal Architecture was introduced by Alistair Cockburn in 2005 in the article "Hexagonal architecture", later refined in his own writings and in subsequent literature. The name is incidental: Cockburn drew the application as a hexagon to make room for several adapters around the core, but the number of sides is not part of the architecture. The accurate name is **Ports and Adapters**.

## What hexagonal solves

Before hexagonal, the dominant layered architectures (presentation, business, data) coupled the business logic to the technologies that surrounded it: the database choice, the HTTP framework, the messaging system. Changing one of those technologies meant rewriting parts of the business logic.

Hexagonal solves this by inverting the relationship:

- The business logic (the **application core**) is the centre of the system and depends on nothing technical.
- Everything technical (database, transport, framework, external system) is an **adapter** plugged into the core through an **interface** (a **port**) that the core defines.
- The core can run, be tested, and evolve without any of those adapters being present.

The result is a system where:

- The core is testable in isolation, with no infrastructure.
- Adapters can be replaced (Postgres for MongoDB, Express for Fastify, REST for GraphQL) without rewriting the core.
- Driving mechanisms can be added (HTTP, CLI, batch, queue consumer) without changing the core.

## What hexagonal is not

- **Not a design method.** Hexagonal does not prescribe how the inside of the core is modelled. The core can be DDD, transaction script, active record, or any other style. Hexagonal only constrains how the core relates to the outside.
- **Not a synonym of DDD.** DDD (Eric Evans, 2003) is a design method for the core. Hexagonal (Cockburn, 2005) is an architecture that surrounds the core. They compose well but solve different problems.
- **Not a synonym of Clean Architecture or Onion Architecture.** Those are sibling architectures with overlapping ideas (the dependency rule, the inward direction) but different vocabulary, different layer counts, and different historical contexts. Each has its own skill.
- **Not a folder layout.** Folders called `domain/`, `application/`, `infrastructure/` are a consequence of hexagonal, not its definition. A project with those folders may still violate the dependency rule and not be hexagonal in any meaningful sense.
- **Not CQRS, Event Sourcing, or EDA.** Those are orthogonal patterns or paradigms that may or may not coexist with hexagonal. None is required.

## When hexagonal applies

Apply hexagonal when:

- The application has a core of business behaviour that must be **independent from the technologies** that deliver it or persist it.
- The application is expected to **outlive its current technical stack**: framework, ORM, transport, or external systems may change.
- The application must be **testable in isolation** from infrastructure, with fast and reliable tests for the core.
- The application has **multiple driving mechanisms** or anticipates them: HTTP, CLI, queue consumers, schedulers, batch processes calling the same core.

## When hexagonal does not apply

Do not apply hexagonal when:

- The application is a **trivial CRUD wrapper** over a database where the framework already provides the structure and the core has no behaviour beyond field validation. Hexagonal adds ceremony without benefit here.
- The application's lifetime is **short** (a migration script, a one-off batch, a proof of concept).
- The team is **too small** to absorb the discipline of ports, adapters, and inward dependencies. The cost of misapplying hexagonal exceeds the benefit.
- The expected core is **a thin facade** over an external service. The whole application is one big adapter; there is no core to isolate.

## How to decide

Ask the following in order:

1. Is there a core of business behaviour that is meaningfully independent from the framework?
2. Is the application expected to live longer than its current stack?
3. Will tests of the core run faster and be more reliable if isolated from infrastructure?
4. Are there (or will there be) multiple driving mechanisms reaching the core?

If two or more answers are no, hexagonal is overkill or premature. Use a simpler architecture (layered, framework-driven) and accept the coupling.

## Common misreadings

- **"Hexagonal is just DDD."** No. DDD models the inside; hexagonal isolates the outside.
- **"If we have a `domain/` folder, we are doing hexagonal."** No. The dependency rule is what makes hexagonal hexagonal. A `domain/` folder that imports the ORM violates the architecture.
- **"Hexagonal requires CQRS or events."** No. Those are independent decisions.
- **"Hexagonal forces three layers."** No. Hexagonal forces a separation between core and outside. Three layers is the most common operational expression but is not in the original definition.
- **"Hexagonal is the same as Clean Architecture."** No. They share the dependency rule but use different vocabulary, different artefacts, and emerged from different traditions. Treat them as cousins, not duplicates.

## What to do when hexagonal does not apply

Use the simplest architecture that fits the actual problem:

- **Framework-driven layered architecture** when the framework already imposes a structure that fits the application.
- **Transaction script** for short workflows with little state.
- **Active record** when the model is one-to-one with the database and there are no rich invariants.

These are not failures. Choosing the right amount of architectural discipline is part of the design.
