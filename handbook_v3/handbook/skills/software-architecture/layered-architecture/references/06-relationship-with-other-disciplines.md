# 06. Relationship with Other Disciplines

Layered architecture is often confused with neighbouring disciplines: other architectures, design methods, patterns, paradigms. This document draws the lines.

The disciplines below are split into two groups:

- **Architectures** (siblings or alternatives to layered).
- **Design methods, patterns, and paradigms** (orthogonal to layered).

---

## Architectures

### Layered vs Hexagonal

Hexagonal (Cockburn, 2005) inverts the dependency direction. In layered, the data layer is at the bottom and its types leak upward. In hexagonal, the domain is at the centre and defines what it needs as ports; the data layer implements those ports.

The shapes look superficially similar (three "layers"), but the rule is opposite:

- Layered: dependencies point downward.
- Hexagonal: dependencies point inward.

Migration from layered to hexagonal is the most common architectural migration. See `software-architecture/references/05-migration-paths.md`.

### Layered vs Clean Architecture

Clean Architecture (Martin, 2012) is hexagonal-flavoured: four concentric circles with the inward dependency rule. Same difference as layered vs hexagonal: clean has the inward rule, layered has the downward rule.

### Layered vs Onion Architecture

Onion (Palermo, 2008) is hexagonal-flavoured: rings with the inward dependency rule. Same difference.

### Layered vs MVC

MVC structures the presentation side of the system. Layered structures the whole system.

In a layered system, MVC may live inside the presentation layer:

```
Presentation:  [MVC: Model, View, Controller]
    |
    v
Business
    |
    v
Data
```

The Model in MVC is a view-side model (DTO, presenter), not the business model. Confusing the two leads to placing business logic in MVC controllers, which violates the layered direction (a controller is presentation, business logic is business).

### Layered vs Microservices

Microservices is a deployment-defined architecture: each service is independently deployable. Layered is a structural pattern within a single deployment unit.

A microservice can be layered internally (a small service often is). A monolith can be layered. The two are independent decisions.

### Layered vs Serverless

Serverless functions are typically too small for a full three-layer separation. A function may have presentation logic (request parsing), business logic, and data access in one file. This is acceptable at very small scale; when the function grows, layered or hexagonal can apply within the function.

### Layered vs Event-Driven Architecture

EDA changes how components communicate (events). Layered structures one component's internals. They compose: a layered component can publish events and consume events.

---

## Design methods, patterns, and paradigms

### Layered and DDD

DDD is a design method for the inside of the core. Layered is an architecture.

- A layered system can host a DDD core in the business layer (or in a domain sublayer).
- The match is weaker than DDD on hexagonal/clean/onion: layered's data layer leaks upward, which erodes the domain over time.
- For long-lived rich-domain systems, the recommendation is to migrate from layered to hexagonal/clean/onion before applying DDD seriously.

### Layered and CQRS

CQRS is a pattern. It can apply within layered:

- Command side: presentation → command handler in business → repository in data.
- Query side: presentation → query handler (often skipping the business layer for thin reads) → read model in data.

Layered does not change because CQRS exists. It just refines what each layer holds.

### Layered and Event Sourcing

Event Sourcing is a persistence pattern. It changes the data layer (events as source of truth instead of state). It does not change the layered structure above.

### Layered and SOLID

SOLID applies inside any layer. The single-responsibility principle of layered (each layer has one purpose) and SRP at the class level are different applications of the same idea. Both matter; neither replaces the other.

### Layered and paradigms

Layered is independent of OO, functional, procedural. The dependency direction is the same regardless.

### Layered and frameworks

Layered is independent of the framework. NestJS, Spring, Laravel, Django all impose layered or near-layered defaults; the architecture is whatever rules the team enforces on top.

---

## Summary

| Discipline | Type | Relationship to layered |
|---|---|---|
| Hexagonal | Architecture (sibling) | Different direction. Pick one. |
| Clean Architecture | Architecture (sibling) | Different direction. Pick one. |
| Onion Architecture | Architecture (sibling) | Different direction. Pick one. |
| MVC | UI structure | Composable inside the presentation layer. |
| Microservices | Architecture (deployment) | Composable; a microservice can be layered internally. |
| Serverless | Architecture (runtime) | Composable for moderately complex functions. |
| Event-Driven Architecture | Paradigm/architecture | Composable; a layered component can publish/consume events. |
| DDD | Design method | Composable but weaker than on hexagonal/clean/onion. |
| CQRS | Pattern | Composable; refines what each layer holds. |
| Event Sourcing | Persistence pattern | Composable; changes the data layer. |
| SOLID | Code-level principle | Orthogonal; applies inside any layer. |
| Functional / OO | Paradigm | Orthogonal. |
| Frameworks | Wiring conventions | Orthogonal; framework lives in presentation and data layers. |

When two of these are in the conversation, treat each as its own decision.
