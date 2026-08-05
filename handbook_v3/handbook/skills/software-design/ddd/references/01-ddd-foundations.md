# 01. DDD Foundations

## What DDD is

Domain-Driven Design is a way of building software where the **domain model** is the centre of the design and every other concern (persistence, framework, transport, UI) is arranged around it.

The model is not a diagram and not a database schema. It is the **executable expression of the business rules** in code, written in the same language the business uses to talk about itself.

DDD has two halves:

- **Strategic.** How the system is divided into coherent regions of meaning (subdomains, bounded contexts) and how those regions relate (context map).
- **Tactical.** How a single bounded context is built internally (entities, value objects, aggregates, domain services, domain events, repositories, factories, application services).

Strategic happens first. Tactical happens inside what strategic produced. Skipping strategic and jumping to tactical is the most common failure mode of DDD adoption.

## What DDD is not

- Not a folder structure.
- Not a code style.
- Not a synonym of hexagonal architecture, clean architecture, onion, or any layered template.
- Not a synonym of CQRS, Event Sourcing, or Event-Driven Architecture.
- Not a way of dividing microservices.
- Not a checklist of artefacts (aggregates, repositories, events) that you must produce.

These things may appear when applying DDD, but DDD is not them. Naming a folder `domain/` does not make a project DDD. Having one repository per aggregate does not make a model DDD. Publishing events does not make integration DDD.

## When DDD applies

Apply DDD when **all** of the following are true:

- The domain has **behaviour beyond CRUD**: rules that decide whether an operation is allowed, calculations that depend on more than the inputs of the current request, invariants that span several attributes or several entities.
- The domain has a **lifecycle**: entities that change state over time and whose history matters.
- The business **owns vocabulary**: words that mean something specific and would be wrong to redefine for technical convenience.
- The domain has **enough complexity to justify the ceremony** of aggregates, ports, adapters, events. A team that introduces this ceremony for a small CRUD pays the cost without the benefit.
- The system is expected to **live long enough** for the model to evolve. Throwaway code does not need DDD.

## When DDD does not apply

Do not apply DDD when:

- The application is **forms over tables**. Read, write, validate format, save. No invariants beyond presence and type.
- The team is **too small** to absorb the strategic discipline. A single developer building a CRUD admin does not need a context map.
- The expected **lifetime of the code is short**. A migration script, a one-off batch, a proof of concept.
- The domain is **fully understood and stable** and matches a generic template (a typical e-commerce admin, a typical CMS). Use the template; the cost of bespoke modelling is not repaid.
- The pressure is **time-to-market for a hypothesis**. Validate first, model later. Premature DDD on an unvalidated product is waste.

When DDD does not apply, the right move is a simpler architecture: layered with transaction script, active record, data mapper without aggregates, or a managed framework that already encodes the patterns you need.

## How to decide

Ask the following questions in order. The first "no" stops the process.

1. Are there business rules in this system that are not just field validation?
2. Does at least one core concept have a non-trivial lifecycle?
3. Does the business use vocabulary that the team must respect verbatim?
4. Will this code live longer than a season?
5. Does the team understand DDD enough to apply it without cargo-culting?

If all five are yes, DDD is a candidate. If two or more are no, DDD is overkill or premature.

A "yes" is not a license to apply DDD everywhere. Inside a system, some subdomains will deserve full DDD modelling and others will not. That decision is part of strategic design (see `02-strategic-design.md`).

## Common misreadings of DDD

- **"DDD is hexagonal architecture."** No. Hexagonal is a way to keep the domain isolated from infrastructure. DDD is what you put inside.
- **"DDD means everything is a class with behaviour."** No. Anemic models are wrong, but not every concept needs behaviour: value objects can be small, simple, immutable.
- **"DDD requires CQRS and events."** No. They are optional patterns that pair well with DDD when the domain demands them.
- **"DDD scales by splitting into microservices."** No. Bounded contexts can live in a monolith. Microservices are a deployment choice, not a modelling choice.
- **"If it has aggregates and repositories, it is DDD."** No. Without ubiquitous language, strategic boundaries, and invariants enforced by the model, those artefacts are decoration.

## What to do when DDD does not apply but the project still needs structure

Use the simplest architecture that fits the actual problem:

- **Transaction script** for short-lived workflows with little state.
- **Active record** when the model is one-to-one with the database and there are no rich invariants.
- **Layered architecture** without aggregates when the domain is shallow but the team values separation.
- **Service-oriented decomposition** without bounded contexts when the cuts are operational rather than semantic.

These choices are not failures. Choosing the right amount of architecture is part of the design.
