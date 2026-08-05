# 05. Anti-patterns

The recurring failures of MVC in real codebases.

## Fat controller

The controller contains business rules, computations, data access, response formatting, all in one place.

- Why it fails: rules in the wrong place. Cannot be reused by another caller (CLI, queue). Tests of the rule require booting the HTTP framework. The controller becomes hundreds of lines of mixed concerns.
- How to recognise: a controller method longer than a screen, or a controller class with many private methods that do business logic.
- Fix: move rules to the application core (use case, service). Move computations to the model or to a domain service. The controller becomes a thin orchestrator: parse, call, build view-model, render.

## Smart view

The view contains business logic: decides what to compute, calls services, mutates state.

- Why it fails: the view is no longer dumb. Logic in the view is not testable without rendering. Two views of the same data implement the rule twice.
- How to recognise: a template or component with `if` statements that depend on business knowledge, or with calls to services or stores.
- Fix: move the logic to the controller or the application core. The result is reflected in the view-model. The view renders.

## Anaemic controller, smart model

The controller is empty (`return Model.find(id).render()`); all the logic is in the model, including persistence, business rules, and presentation hints.

- Why it fails: the model becomes a god object. It mixes data, behaviour, persistence, and presentation. Testing requires the full stack.
- How to recognise: a model class with hundreds of methods covering everything from validation to template-specific accessors.
- Fix: split. Persistence to the data layer (or the repository in hexagonal/clean/onion). Business rules to domain services or use cases. Presentation hints to the view-model or the controller.

## Domain model as MVC model in a larger system

The MVC model is the domain aggregate. The controller passes the aggregate directly to the view; the view renders the aggregate's fields.

- Why it fails: domain types leak to the wire. A change in the domain model breaks the wire format. Every consumer of the wire format gets every field of the aggregate, including ones that should not be exposed.
- How to recognise: JSON responses that look exactly like the aggregate's internal shape. Templates that read aggregate methods directly.
- Fix: build a view-model. The controller maps the aggregate's relevant fields into a DTO; the view reads the DTO.

## Skipping the application core

The controller calls the repository directly, bypassing the use case (in a hexagonal/clean/onion system) or the business layer (in layered).

- Why it fails: business rules that should apply do not. Two callers reaching the same data through different paths get different behaviour. The architecture below MVC is undermined.
- How to recognise: a controller imports a repository or a DAO and queries it directly.
- Fix: route through the application core. Even if the use case is one line, that one line is the place where rules can be added without changing the caller.

## "MVC" as the answer to "what is the architecture?"

The team responds to "what is the architecture of this backend?" with "MVC". The backend has business logic, multiple bounded contexts, and a real domain.

- Why it fails: MVC structures the UI side. It does not answer the architectural question for the backend. The actual architecture (hexagonal, layered, etc.) is unstated and likely accidental.
- How to recognise: the only architectural vocabulary in the team is "controller, view, model".
- Fix: pick a backend architecture explicitly. MVC may still live inside the HTTP driving adapter, but the architecture is named separately.

## Controller per route, no use cases

The system has hundreds of controllers, each handling one HTTP route, each containing the full body of the operation. There are no use cases or services beyond the controllers.

- Why it fails: the architecture is "everything in controllers". MVC has degenerated into a routing convention. Reuse is impossible.
- How to recognise: a `controllers/` folder with hundreds of files and almost nothing in `services/` or `usecases/`.
- Fix: extract use cases. The controller becomes thin; the use case holds the logic.

## Fat view-model with logic

The view-model contains methods that compute on the fly, call services, or branch on business state.

- Why it fails: the view-model has acquired logic that should be elsewhere. Tests of the view-model require dependencies it should not have. Two views of the same data implement the same computation twice (or differently).
- How to recognise: a view-model class with methods beyond simple getters.
- Fix: pre-compute in the controller (or in the application core). The view-model holds plain data.

## Per-widget MVC inflation

In classical desktop MVC tradition, every widget has its own MVC triplet. Applied to web MVC or to modern frontends, this produces an explosion of controllers and models for trivial UI elements.

- Why it fails: ceremony. Trivial widgets do not need three classes.
- How to recognise: a controller per button.
- Fix: scope MVC to meaningful UI surfaces (a page, a feature), not every widget.

## Mixing variants

The codebase is described as MVC, but parts use MVP (passive view, presenter), parts use MVVM (bindings), parts use Flux (actions, reducers).

- Why it fails: contributors do not know which pattern applies where. Reviewers cannot agree on rules.
- How to recognise: vocabulary that drifts between files (controller here, presenter there, store there).
- Fix: pick one variant for the codebase. Migrate or refactor the others.

## "Convention is the architecture"

The team relies on the framework's MVC convention (Rails, Django, NestJS controllers) without thinking about responsibilities. New code is placed by the framework's prompt; rules are not enforced.

- Why it fails: the framework's defaults are its opinion. They handle some responsibilities by accident; others slip through.
- How to recognise: there is no explicit document about what controllers, views, and models do in this project.
- Fix: write the responsibilities down. Enforce them in code review.

## Output

When reviewing an MVC codebase, look for these patterns by name. The most damaging are fat controllers, domain-as-MVC-model, and skipping the application core, because they collapse MVC into "code in controllers" or undermine the architecture below.
