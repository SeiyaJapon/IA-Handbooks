# 07. Relationship with Other Architectures

Clean is one of three sibling architectures (hexagonal, clean, onion) built around The Dependency Rule. They share the inward direction but **differ in vocabulary, prescribed artefacts, and where the line is drawn between layers**.

This document draws those lines for clean. The goal is to make the differences explicit so a clean codebase does not drift into hexagonal or onion vocabulary by accident.

## Clean vs Hexagonal (Cockburn 2005)

### What is shared

- The Dependency Rule. Source code dependencies point inward.
- The domain at the centre, framework-free.
- Inversion through interfaces.

### What differs

| Aspect | Clean (Martin) | Hexagonal (Cockburn) |
|---|---|---|
| Shape | Four concentric circles | Hexagon with two regions (inside / outside) and three operational layers |
| Inner-circle naming | Entities, Use Cases | "Application core", "domain" |
| Use case artefact | Interactor with explicit Input Boundary, Output Boundary, Input Data, Output Data | "Use case" or "application service", less prescribed |
| Adapter naming | Three named roles: Controller, Presenter, Gateway | Two kinds: driving adapter, driven adapter |
| Interface naming | Boundaries (Input/Output), Gateway interfaces | Ports (driving/driven) |
| Output side | Presenter implements Output Boundary; Interactor calls it | Driving adapter typically receives the result and formats it |
| Outer circle | "Frameworks & Drivers" : explicitly named | "Infrastructure" : single bucket |

### Why the difference matters

Clean prescribes more. The Output Boundary + Presenter pattern is clean's specific signature: the Interactor talks to the Presenter through an interface, and the Presenter builds the View Model. Hexagonal does not prescribe this; it leaves the formatting to the driving adapter.

Clean splits adapters into three named roles (Controller / Presenter / Gateway). Hexagonal calls them driving and driven without further specialisation.

### Practical guidance

- Use clean vocabulary throughout a clean codebase. Do not say "port" when you mean "Boundary"; do not say "driving adapter" when you mean "Controller".
- A clean codebase that drifts into hexagonal vocabulary loses the clean-specific clarity (Boundaries, Presenter substitution).
- A hexagonal codebase that uses clean vocabulary takes on artefacts (explicit Output Boundary) it did not need.

## Clean vs Onion (Palermo 2008)

### What is shared

- The Dependency Rule.
- The domain at the centre.
- Inversion through interfaces declared by inner layers.

### What differs

| Aspect | Clean (Martin) | Onion (Palermo) |
|---|---|---|
| Shape | Four concentric circles | Concentric rings (typically four) |
| Domain partition | One Entities circle holds all domain | Domain Model ring + Domain Services ring (separated) |
| Application partition | One Use Cases circle | Application Services ring |
| Adapter naming | Controller, Presenter, Gateway | "External layer" without role names |
| Use case artefact | Interactor + Boundaries + Data | Application Service (less prescribed) |

### Why the difference matters

Onion explicitly separates **Domain Services** from **Domain Model** as different rings. Clean does not. A clean codebase puts what onion calls Domain Services into Entities (if enterprise-wide) or Use Cases (if application-specific).

Onion does not name Controller, Presenter, Gateway as distinct roles. Clean does.

### Practical guidance

- A clean codebase does not have a "Domain Services ring". If cross-entity domain logic exists, it lives inside Entities (a domain service in the DDD sense, in clean's Entities circle) or inside Use Cases.
- A clean codebase does not call its application code "Application Services". The artefact is the **Interactor**.

## Clean vs Layered (classical)

Layered points dependencies downward (presentation → business → data). Clean points inward.

Migration from layered to clean is the same shape as layered to hexagonal/onion: invert the dependency direction by introducing Boundaries (interfaces) declared by the inner layers and implemented by the outer.

## Clean vs DDD

Different disciplines.

- DDD is a design method for the inside of the core (Entities and Use Cases in clean).
- Clean is the structure surrounding the core.

A clean codebase can host a DDD core. A clean codebase can host a non-DDD core. They compose; neither requires the other.

In clean + DDD:

- DDD's aggregates and value objects live in **Entities**.
- DDD's domain services (when needed) live in **Entities**.
- DDD's application services correspond to clean's **Interactors**.

The clean structure and the DDD modelling are independent decisions.

## Clean vs MVC

MVC is a UI structure. In a clean codebase, MVC may live inside the HTTP Controller / Presenter / View on the Interface Adapters circle.

Note: clean already has a "Controller" and a "Presenter". MVC's Controller and View map naturally to these. MVC's Model is a view-model (what clean calls View Model), not an Entity.

## Clean vs CQRS

CQRS is a pattern. In clean, command Use Cases and query Use Cases are separate Interactors. Each has its own Boundaries.

CQRS does not change clean's structure. It refines what each Use Case does.

## Clean vs Event Sourcing

Event Sourcing changes how Gateways persist state. The inner circles are unchanged.

## Clean vs Microservices

Composable. A microservice can be clean internally.

## Clean vs Serverless

A serverless function can host a clean structure: the function handler is part of Interface Adapters (a Controller, possibly with the Presenter logic merged for simplicity); the inner circles live inside the function or in a shared library.

## Summary

| Architecture | Type | Relationship |
|---|---|---|
| Hexagonal | Sibling architecture | Same Dependency Rule, different vocabulary. Pick one. |
| Onion | Sibling architecture | Same Dependency Rule, different ring partition. Pick one. |
| Layered | Predecessor | Migrate to clean by inverting direction. |
| MVC | UI structure | Composable inside Interface Adapters. |
| Microservices | Deployment | Composable. |
| Serverless | Runtime | Composable. |
| EDA | Communication paradigm | Composable. |
| DDD | Design method | Composable. Lives inside Entities and Use Cases. |
| CQRS | Pattern | Composable. |
| Event Sourcing | Persistence pattern | Composable; affects Gateways. |

## The disciplinary line for clean

If a discussion mentions:

- "ports" or "driving/driven adapters" → hexagonal vocabulary. In a clean codebase, translate to "Boundaries" and "Controller / Presenter / Gateway".
- "Domain Services as a separate ring" → onion vocabulary. In a clean codebase, those concepts fold into Entities or Use Cases.
- "rings" → onion vocabulary. In a clean codebase, the term is "circles".

Mixing vocabularies signals that two architectures are being applied at once, which is not possible. Pick one and use its vocabulary consistently.
