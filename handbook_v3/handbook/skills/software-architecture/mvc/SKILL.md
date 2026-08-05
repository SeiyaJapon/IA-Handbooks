# MVC Skill

Use this skill when designing, reviewing, or refactoring software using Model-View-Controller, especially when the user mentions MVC, controllers, views, models in the MVC sense, or when reasoning about the structure of a UI or web framework.

## What MVC is

MVC (Model-View-Controller) is a **structural pattern for the user-interface side of a system**. It splits the presentation responsibilities into three roles:

- **Model**: the data and the rules of the data, from the UI's point of view.
- **View**: the visual presentation of the data.
- **Controller**: the mediator that handles input and updates model and view.

MVC was introduced by Trygve Reenskaug in 1979 (Xerox PARC) for Smalltalk-80. Since then, the term has been used for many similar but distinct patterns (MVP, MVVM, web MVC, classical desktop MVC).

## What MVC is and is not in this skill

MVC has two valid uses today:

1. **As a UI structure inside a driving adapter**, in a hexagonal/clean/onion or layered codebase. The MVC pieces live inside the presentation side and structure how requests are handled.
2. **As the system-wide architecture** of a small, UI-heavy application with little business logic outside the UI (some desktop or simple web apps).

MVC is NOT:

- A synonym of the canonical three-layer architecture (presentation/business/data). They are different decisions on different axes.
- A synonym of MVP, MVVM, or any other variant. They share the spirit but are different patterns.
- The architecture of a long-lived rich-domain backend system.
- "Whatever a web framework does that has controllers".

## Non-negotiable rule

Never start MVC work from "what controllers will we have".

Always start from the **UI shape**: what the user sees, what the user does, what data needs to flow.

The mandatory order is:

1. Identify the UI surfaces (screens, pages, widgets).
2. For each surface, identify the data shown (the model from the UI's perspective).
3. Identify the user actions (the controller's input).
4. Decide how the view renders the model.
5. Decide how the controller updates the model in response to actions.
6. Only then discuss frameworks, routes, templates, and concrete technologies.

## Hard rules

### Three roles, no more no less

- **Model**: data + rules of the data (from the UI's perspective).
- **View**: rendering of the model.
- **Controller**: input handling, model mutation, view selection.

A class that mixes roles (a controller that renders, a view that mutates state, a model that knows about HTTP) is a violation.

### MVC is UI-side

In modern systems, MVC lives on the presentation side. The "model" in MVC is the **view-model** or the application's read-side representation, not the domain model of the business.

When MVC is used inside a hexagonal/clean/onion or layered codebase:

- The MVC controller is the driving adapter (HTTP controller, GUI event handler).
- The MVC model is a DTO or view-model populated from the application core.
- The MVC view is the rendering (HTML template, React component, native widget).

The business domain is **outside** MVC. The MVC controller calls into the application core (use cases, services); the core does the work; the MVC controller maps the result into the MVC model and selects the view.

### Controllers are thin

Controllers handle input, call the model or the application core, select the view. They do not contain business rules. They do not query databases.

### Views are dumb

Views render. They do not mutate state. They do not call services. They display the model.

### Models in MVC are not domain models

The MVC model is what the view shows. In simple apps it may overlap with a domain model; in complex apps it is a separate view-model.

## Forbidden shortcuts

Do not say:

- "MVC is the architecture of any web framework that has controllers."
- "Controllers can hold business rules; that is what controllers are for."
- "The MVC model is the same as the domain model."
- "MVC and three-tier layered are the same thing."
- "MVP, MVVM, and MVC are interchangeable names."

If a discussion about MVC slides into "where do the business rules live", stop. Business rules do not live in MVC. They live in the application core; MVC is the UI shell around that core.

## Mandatory review behavior

When reviewing an MVC codebase, check in order:

1. Are the three roles (model, view, controller) clearly identified?
2. Are controllers thin, free of business rules, free of direct data access?
3. Are views dumb, free of state mutation and service calls?
4. Are models view-side (DTOs, view-models) and not confused with domain entities?
5. Is MVC operating at the right scope (UI-side of a larger system, or system-wide for a small UI app)?
6. If the system has business logic beyond UI, is there an architecture below MVC (hexagonal, layered) where that logic lives?

## When to pick MVC

- **Inside a driving adapter** of a hexagonal/clean/onion or layered system, when the UI side benefits from MVC's separation. Most modern uses fall here.
- **As the system-wide architecture** of small UI-heavy apps with little business logic outside the UI. Examples: simple desktop tools, basic admin UIs, prototypes.

## When NOT to pick MVC

- As the architecture of a backend service with business logic. The business does not belong in MVC; pick a backend architecture (hexagonal, clean, layered) and use MVC only inside the HTTP driving adapter if at all.
- As a substitute for thinking about the system's architecture. "We use MVC" does not answer "what is the architecture of this backend?".
- For long-lived systems where the model has rich invariants. MVC's model is UI-centric; rich domains need a separate domain model.

## Variants briefly

- **MVC (Smalltalk-80, classical).** The view observes the model; the controller updates the model; the model notifies the view. Used in some desktop UIs.
- **MVC (web).** The controller receives the HTTP request, calls the model, picks a view template, returns rendered HTML. Used in Rails, Django, classical PHP frameworks.
- **MVP (Model-View-Presenter).** The view is passive; the presenter mediates between view and model. Common in some desktop and mobile UIs.
- **MVVM (Model-View-ViewModel).** The view binds to a view-model that exposes properties and commands; the view-model talks to the model. Common in WPF, modern frontend frameworks.

This skill covers MVC. MVP and MVVM are not separate skills in this handbook; if a project uses them, the same principles apply (separation of presentation roles, thin controllers/presenters, dumb views, view-side models).

## References

Load these when needed:

- `references/01-foundations.md` for what MVC is, its origin, when it applies, when it does not.
- `references/02-roles-and-responsibilities.md` for the model, view, and controller roles in detail.
- `references/03-mvc-inside-larger-architectures.md` for how MVC composes with hexagonal/clean/onion/layered as a UI structure.
- `references/04-variants.md` for MVP, MVVM, web MVC, classical MVC, and how to tell them apart.
- `references/05-anti-patterns.md` for the recurring failures (fat controllers, smart views, business in MVC).
- `references/06-relationship-with-other-disciplines.md` for MVC vs other architectures, design methods, patterns.
- `references/07-review-checklist.md` when reviewing an MVC codebase.
- `references/08-user-preferences.md` for Francisco's project conventions.

Important: MVC is a UI structure. Do not load this skill when the question is about backend architecture in general; load `software-architecture/SKILL.md` first.
