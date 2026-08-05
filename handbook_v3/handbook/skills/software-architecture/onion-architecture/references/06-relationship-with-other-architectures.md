# 06. Relationship with Other Architectures

Onion is one of three sibling architectures (hexagonal, clean, onion) built around the inward-dependency principle. They share the inward direction but **differ in vocabulary, ring partition, and what they prescribe**.

This document draws those lines for onion. The goal is to make the differences explicit so an onion codebase does not drift into hexagonal or clean vocabulary by accident.

## Onion vs Hexagonal (Cockburn 2005)

### What is shared

- Inward-only dependency rule.
- The domain at the centre, framework-free.
- Inversion through interfaces.

### What differs

| Aspect | Onion (Palermo) | Hexagonal (Cockburn) |
|---|---|---|
| Shape | Concentric rings | Hexagon with two regions (inside / outside) and three operational layers |
| Inner rings | Domain Model, Domain Services, Application Services | "Application core" (single concept, not split) |
| Domain Services as separate ring | Yes : onion's distinguishing feature | No : folded into the application core |
| Outer naming | External Layer | Infrastructure |
| Adapter roles | Not named | Named (driving / driven) |
| Interfaces | "Interfaces declared by inner rings" | "Ports" |

### Why the difference matters

The most important difference is **the explicit Domain Services ring**. Onion separates intrinsic domain logic (Domain Services) from application orchestration (Application Services). Hexagonal does not.

This makes onion a heavier fit when there is no cross-entity domain behaviour (the ring would be empty), and a better fit when there is a lot of it (the ring shines).

The second important difference is the **External Layer naming and the absence of adapter roles**. Hexagonal calls them driving/driven adapters; onion calls them all "External Layer" without further role names.

### Practical guidance

- Use onion vocabulary throughout an onion codebase. Do not say "port" when you mean "interface declared by an inner ring"; do not say "driving adapter" when you mean "External Layer entry".
- An onion codebase that drifts into hexagonal vocabulary loses the onion-specific clarity (Domain Services as a separate ring).
- A hexagonal codebase that uses onion vocabulary takes on a ring (Domain Services) that hexagonal does not prescribe.

## Onion vs Clean (Martin 2012)

### What is shared

- Inward-only dependency rule.
- The domain at the centre, framework-free.
- Inversion through interfaces declared by inner layers.

### What differs

| Aspect | Onion (Palermo) | Clean (Martin) |
|---|---|---|
| Shape | Rings | Concentric circles |
| Inner naming | Domain Model + Domain Services + Application Services | Entities + Use Cases |
| Domain Services as separate layer | Yes | No (folded into Entities or Use Cases) |
| Use case structure prescribed | No | Yes (Interactor + Input/Output Boundary + Input/Output Data) |
| Outer adapter roles | Not named | Three named (Controller / Presenter / Gateway) |
| Outer naming | External Layer | Frameworks & Drivers + Interface Adapters (split) |

### Why the difference matters

Onion has the explicit Domain Services ring; clean does not.

Clean prescribes the Use Case structure (Interactor + Boundaries + Data); onion does not : Application Services is a generic ring without prescribed artefact structure.

Clean splits the outer area into Interface Adapters and Frameworks & Drivers, with three named roles in the former; onion keeps it as one External Layer.

### Practical guidance

- An onion codebase does not have "Interactors" or "Boundaries". Application Services in onion are use cases, but without the prescribed artefact structure.
- An onion codebase does not split adapters into Controller / Presenter / Gateway. They live in the External Layer; sub-folders by capability are convention only.

## Onion vs Layered (classical)

Layered points dependencies downward (presentation → business → data). Onion points inward.

Migration from layered to onion is the same shape as layered to hexagonal/clean: invert the dependency direction by introducing interfaces declared by the inner layers and implemented by the outer.

The onion-specific decision in such a migration: identify whether the domain has cross-entity behaviour worth a Domain Services ring. If yes, onion fits. If no, hexagonal or clean fit better.

## Onion vs DDD

Different disciplines.

- DDD is a design method for the inside of the core (Domain Model and Domain Services in onion).
- Onion is the structure surrounding the core.

An onion codebase can host a DDD core. An onion codebase can host a non-DDD core. They compose; neither requires the other.

In onion + DDD:

- DDD's aggregates live in **Domain Model**.
- DDD's value objects live in **Domain Model**.
- DDD's domain services correspond to onion's **Domain Services ring** : the alignment is direct and is one reason onion and DDD pair well.
- DDD's application services correspond to onion's **Application Services ring**.

The onion structure and the DDD modelling are independent decisions, but their vocabularies align more directly than hexagonal-and-DDD or clean-and-DDD.

## Onion vs MVC

MVC is a UI structure. In an onion codebase, MVC may live inside the External Layer (the HTTP entry point).

## Onion vs CQRS, Event Sourcing

Patterns. Composable.

CQRS: command Application Services and query Application Services may be separate. Onion's structure is unchanged.

Event Sourcing: changes how repositories in External Layer work. Inner rings unchanged.

## Onion vs Microservices, Serverless, EDA

Composable. A microservice can be onion internally. A serverless function can host onion. An EDA system can have onion-shaped components.

## Summary

| Architecture | Type | Relationship |
|---|---|---|
| Hexagonal | Sibling architecture | Same dependency rule, different vocabulary, no Domain Services ring. Pick one. |
| Clean | Sibling architecture | Same dependency rule, different vocabulary, no Domain Services ring, prescribes Use Case structure and adapter roles. Pick one. |
| Layered | Predecessor | Migrate to onion by inverting direction. |
| MVC | UI structure | Composable inside External Layer. |
| Microservices | Deployment | Composable. |
| Serverless | Runtime | Composable. |
| EDA | Communication paradigm | Composable. |
| DDD | Design method | Composable. Onion's rings align directly with DDD's tactical patterns. |
| CQRS | Pattern | Composable. |
| Event Sourcing | Persistence pattern | Composable; affects repositories in External. |

## The disciplinary line for onion

If a discussion mentions:

- "ports" or "driving/driven adapters" → hexagonal vocabulary. In an onion codebase, translate to "interfaces declared by inner rings" and "External Layer entries".
- "Interactor", "Input Boundary", "Output Boundary", "Controller / Presenter / Gateway" → clean vocabulary. In an onion codebase, those concepts do not appear; Application Services is a generic ring.
- "circles" → clean vocabulary. In an onion codebase, the term is "rings".

Mixing vocabularies signals that two architectures are being applied at once, which is not possible. Pick one and use its vocabulary consistently.

## Choosing between onion, hexagonal, clean

A simple rule:

- **Empty Domain Services ring**: pick hexagonal or clean. Onion is heavier than necessary.
- **Rich cross-entity domain logic + want to prescribe Use Case artefacts and adapter roles**: pick clean.
- **Rich cross-entity domain logic, prefer minimal outer prescription**: pick onion.
- **Want lightest of the three, ports/adapters vocabulary, no need for explicit Use Case structure or Domain Services ring**: pick hexagonal.

The choice is often one of vocabulary preference and the value the team places on each prescription.
