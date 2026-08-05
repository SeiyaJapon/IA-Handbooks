# 03. MVC Inside Larger Architectures

The most common modern use of MVC is **inside the presentation side of a larger system**: hexagonal, clean, onion, or layered. This document describes how MVC composes with each of those, what each MVC piece corresponds to in the larger architecture, and where business logic lives.

## MVC inside hexagonal

In a hexagonal codebase:

- The HTTP **driving adapter** is the MVC **controller**.
- The MVC **model** is a DTO or view-model populated from the use case's result. It is **not** the domain aggregate.
- The MVC **view** is the rendering: HTML template, JSX component, native widget.
- The use case (the driving port) is **outside MVC**. It is the application core.

```
HTTP request
   |
   v
Driving adapter / MVC controller
   |
   v  (deserialise to command)
Application core (use case)
   |
   v  (returns result)
Driving adapter builds View-Model
   |
   v
View renders
   |
   v
HTTP response
```

Where business logic lives:

- In the **domain** (entities, aggregates, value objects, domain services).
- Or in the **application core** (use cases, application services).
- Never in the MVC controller.
- Never in the MVC view.
- Never in the MVC model (because the MVC model is a view-model, not a domain model).

Common errors when MVC meets hexagonal:

- The controller imports the domain aggregate and uses it as the view-model directly. Leak. Build a separate view-model.
- The controller calls the repository directly, bypassing the use case. Skipping the application core breaks the architecture.
- The view contains conditionals that should be domain rules. Those decisions belong inside the aggregate.

## MVC inside clean architecture

Clean Architecture has four circles: Entities, Use Cases, Interface Adapters, Frameworks & Drivers.

- The MVC **controller** is part of **Interface Adapters**.
- The MVC **view** is part of **Interface Adapters** (presenter / view) or **Frameworks & Drivers** depending on how the team draws the line.
- The MVC **model** is the View Model defined by the Interface Adapters layer.
- The Entities and Use Cases layers are **outside MVC**.

The composition is the same as hexagonal in spirit; the vocabulary is different.

## MVC inside onion architecture

Onion has rings: Domain Model, Domain Services, Application Services, External Layer.

- The MVC pieces live in the **External Layer**.
- The MVC controller calls Application Services.
- The MVC view-model is populated from Application Services' output.

Same composition, different vocabulary.

## MVC inside layered architecture

In a classical layered system:

- The MVC pieces live in the **presentation layer**.
- The MVC controller calls the **business layer**.
- The MVC model is populated from the business layer's response.
- The MVC view renders the result.

Here the gap between MVC and the rest is smaller because layered's presentation layer was already shaped to host MVC. Many web frameworks (Rails, Django, classical PHP) are layered with MVC inside.

## Where business logic lives

In every case:

```
Business logic --> Outside MVC.
   - In the domain (entities, aggregates).
   - In the application core (use cases, services).
   - In the business layer (in classical layered).
```

If business logic is found inside MVC (controller, view, or model in a larger system), it has escaped its proper home. Move it.

## How to structure the controller

A controller in this composition has a thin body:

1. Parse the HTTP request (or GUI event) into a command/query.
2. Validate structural input (format, presence).
3. Call the application core (use case, service).
4. Translate the result into a view-model.
5. Select and render the view.
6. Return the response.

If the body grows to include rule decisions, computations, or data access, those have escaped from the application core.

## How to structure the view

The view in this composition is dumb:

- It receives a view-model.
- It renders.
- It does not call services.
- It does not mutate state.

Conditionals in the view are limited to formatting (show this label vs that label, show empty state if list is empty). Conditionals that require business knowledge belong in the application core, with the result reflected in the view-model.

## How to structure the view-model

The view-model is shaped by what the view needs:

- Pre-computed values (formatted dates, derived totals, status labels).
- Flattened data (joins of multiple aggregates into one shape).
- UI-relevant flags (canEdit, isHighlighted, hasChildren).

It is built by the controller from the application core's result. It is not the domain aggregate. It is not the database row.

## Anti-patterns specific to MVC inside larger architectures

- **Controller as use case.** The controller does the orchestration that should be in the application core. Move the orchestration to a use case; the controller calls the use case.
- **Domain aggregate as view-model.** The controller returns the aggregate as JSON. Domain types leak to the wire. Build a view-model.
- **View calls service.** The view template or component contains a call to the application core. Move the call to the controller; pass the result via the view-model.
- **Model with persistence concerns.** The MVC model has methods to save/load. In a larger architecture, persistence is outside MVC. Those methods belong in repositories.

## Output

When MVC is inside a larger architecture, the team can answer:

- What is the larger architecture?
- Where does each MVC piece sit in that architecture?
- Where does business logic live (and is it kept outside MVC)?
- What is the view-model, and how is it built?

Without these answers, MVC absorbs work that belongs elsewhere, and the larger architecture is undermined from inside the presentation side.
