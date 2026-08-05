# 08. User Preferences (Francisco)

Project conventions for MVC in Francisco Pérez's projects at Kintai.

## When MVC is acceptable

MVC is acceptable in this project's repos when:

- It lives **inside the HTTP driving adapter** of a hexagonal/clean/onion/layered backend. This is the modern composition.
- It is the **system-wide architecture** of a small UI app (admin tool, prototype, simple desktop utility).

## When MVC is rejected

MVC is rejected as the system-wide architecture for any backend service with real business logic. The backend has its own architecture (default: hexagonal); MVC may live inside the HTTP adapter, but the architecture is not "MVC".

## Roles

- **Controllers** are thin. Their body parses input, calls the application core (or the model in small apps), builds a view-model, selects a view.
- **Views** are dumb. They render. They do not call services, do not mutate state, do not contain business rules.
- **Models** in a larger architecture are view-models / DTOs. They are not the domain model.

## Composition with hexagonal (the default backend architecture)

In the default composition:

- The HTTP controller is both the driving adapter and the MVC controller.
- The use case (driving port) is outside MVC.
- The view-model is built by the controller from the use case's result.
- The view (HTML, JSON serialisation) renders the view-model.

The domain aggregate is **never** used as the view-model. View-models are explicit DTOs.

## Variant policy

- **Web MVC** inside hexagonal is the default for backend HTTP adapters.
- **Classical MVC** (active model, observers) is not used in this project's code.
- **MVP and MVVM** are accepted in frontend code if the framework expects them; they are not mixed with MVC in the same module.
- **Flux/Redux-style** frontend state is its own decision; not labelled MVC.

## Anti-patterns to flag immediately

- Fat controllers with business rules.
- Domain aggregates passed directly as JSON responses.
- Controllers that import repositories and bypass the use case.
- Views that call services or mutate state.
- "MVC" as the answer to "what is the architecture of this backend".

## Communication style

- Avoid em dash in normal prose. When adding an aside or clarification, prefer parentheses or a separate sentence. Do not use em dash as a default punctuation device. Only keep em dash when quoting existing text or when explicitly discussing the character itself.
- Code comments are in English and minimal.

## Open conventions (to confirm)

- Whether view-models are explicit classes or plain object literals.
- Whether the controller/use case/view-model/view boundaries are enforced by lint rules or only by review.
- Whether MVC vocabulary is used in code identifiers (`*Controller`, `*View`) or only in conversation.

These are open. Do not assume; ask before treating any of them as a rule.
