# 06. Relationship with Other Disciplines

MVC is constantly conflated with neighbouring disciplines. This document draws the lines.

## MVC vs other architectures

### MVC vs Hexagonal / Clean / Onion

MVC is a UI structure. Hexagonal/Clean/Onion are system architectures.

- They compose: MVC lives inside the HTTP driving adapter; the application core is outside MVC.
- They are not alternatives at the same level. Choosing hexagonal does not exclude MVC; choosing MVC does not exclude hexagonal.

### MVC vs Layered

MVC structures the presentation side. Layered structures the whole system.

- MVC lives inside the presentation layer of a layered system.
- A layered system without MVC is possible (the presentation layer can be structured differently).
- An MVC system without explicit layered is possible if the app is small (UI + thin business + storage).

### MVC vs Microservices

Independent decisions. A microservice can use MVC inside its HTTP adapter. A monolith can use MVC inside its presentation. Microservices is about deployment; MVC is about UI structure.

### MVC vs Event-Driven Architecture

EDA changes how components communicate. MVC structures the UI of one component. They compose: an EDA component can have a UI, and that UI may be MVC.

### MVC vs Serverless

A serverless function with a UI (a server-rendered HTML response) can have MVC inside. The function handler is the controller; the response is the view; the view-model is built from the function's logic.

## MVC vs design methods, patterns, paradigms

### MVC and DDD

Different layers entirely.

- DDD models the domain (entities, aggregates, value objects, domain services).
- MVC structures the UI.
- The MVC model is **not** the domain model. In a system with both, the MVC model is a view-model populated from the domain via the application core.

### MVC and CQRS

CQRS shapes the application core (commands and queries). MVC structures the UI calling the core.

- Web MVC controllers map naturally to CQRS handlers: GET endpoints call queries, POST/PUT/DELETE call commands.
- The mapping is operational, not architectural; CQRS does not require MVC, MVC does not require CQRS.

### MVC and Event Sourcing

Event Sourcing changes how the model is persisted. The view-side reads projections. MVC continues to work above the projections.

### MVC and SOLID

SOLID applies inside any class, including controllers, views, and models. They are orthogonal.

### MVC and frameworks

Frameworks (Rails, Django, NestJS, ASP.NET MVC) often impose an MVC-shaped structure. The framework is not the architecture; MVC is the structural pattern, the framework is the wiring convention.

## Variants of MVC vs MVC

Already covered in `04-variants.md`. Quick summary:

- **MVP:** passive view, presenter mediates.
- **MVVM:** bindings, ViewModel exposes properties and commands.
- **Flux/Redux:** unidirectional data flow, single store. Not MVC.

These share the spirit of separating UI roles but differ in how the roles communicate. Pick one per codebase; do not mix.

## Summary

| Discipline | Type | Relationship to MVC |
|---|---|---|
| Hexagonal / Clean / Onion | System architecture | Composable; MVC inside the driving adapter. |
| Layered | System architecture | Composable; MVC inside the presentation layer. |
| Microservices | Deployment architecture | Composable; MVC inside each service's UI. |
| Serverless | Runtime architecture | Composable; MVC inside the function handler. |
| Event-Driven Architecture | Communication paradigm | Composable; orthogonal to UI. |
| DDD | Design method | Orthogonal; MVC model is not domain model. |
| CQRS | Pattern | Composable; controllers map to handlers. |
| Event Sourcing | Persistence pattern | Composable; views read projections. |
| MVP | Pattern variant | Sibling; pick one. |
| MVVM | Pattern variant | Sibling; pick one. |
| Flux / Redux | State pattern | Different family. |
| SOLID / code quality | Code-level principle | Orthogonal. |
| Frameworks | Wiring conventions | Orthogonal. |

When MVC vocabulary appears in a discussion that is not about the UI, route the discussion to the appropriate architectural skill.
