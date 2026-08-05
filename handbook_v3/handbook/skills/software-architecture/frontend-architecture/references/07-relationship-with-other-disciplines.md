# 07. Relationship with Other Disciplines

## Frontend architecture vs MVC / MVP / MVVM

These are UI structuring patterns. They live inside frontend architecture, not in place of it.

- A React app may use MVVM internally (components are views; view-models are hooks or stores).
- A Vue app uses MVVM (the framework's binding model).
- An Angular app uses a mix (components, services, change detection).

Frontend architecture is the system-level decision: features, modules, state, data flow, routing. MVC/MVP/MVVM is one part of it (how UI roles are split inside a feature).

## Frontend architecture vs Flux / Redux / Elm

Flux/Redux/Elm are state management patterns: unidirectional data flow, single store, actions/reducers.

They are a choice within frontend architecture (specifically, in state management). Adopting Redux does not answer "how are features organised" or "how does data flow from API".

## Frontend architecture vs framework

Frameworks (React, Vue, Angular, Svelte) impose conventions. Each shapes some architectural decisions:

- Angular has strong opinions on modules, services, dependency injection.
- React has weaker conventions; more architectural decisions are explicit.
- Vue is mid-way.

The framework is part of the architecture but does not define it.

## Frontend architecture vs backend architecture

Backends (hexagonal, clean, onion, layered) and frontends share some ideas (boundaries, isolation, dependency direction) but the forces differ:

- Frontends have UI as the primary actor (a user clicking).
- Backends have systems as actors (other services, scheduled jobs).
- Frontends have bundle size as a cost; backends have memory and CPU.
- State management is a frontend's defining concern; backends rarely have the same shape.

Mapping backend architectures onto frontends often produces over-engineering. Frontends benefit from their own architectural family.

## Frontend architecture vs DDD

DDD is a backend design method. Frontends benefit from DDD vocabulary (bounded contexts as features, ubiquitous language) but typically do not implement aggregates and repositories on the client.

## Frontend architecture vs accessibility, performance, design

Accessibility, performance, and design are first-order concerns of any frontend. They constrain the architecture but do not replace it.

## Summary

| Discipline | Type | Relationship |
|---|---|---|
| MVC / MVP / MVVM | UI structuring patterns | Inside a feature. |
| Flux / Redux / Elm | State management patterns | One choice within state management. |
| Framework (React, Vue, Angular) | Wiring conventions | Shapes architecture; does not define it. |
| Backend architecture | System architecture | Different forces; do not map directly. |
| DDD | Design method | Vocabulary borrowable; tactical patterns rarely transfer. |
| Accessibility, performance, design | Cross-cutting concerns | Constrain the architecture. |
