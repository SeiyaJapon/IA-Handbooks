# 02. Roles and Responsibilities

The three roles of MVC are model, view, and controller. This document defines each role, what it owns, what it does not own, and how the roles interact.

## Model

The model is the **data and the rules of the data**, from the UI's point of view.

### Responsibilities

- Hold the data the view needs to render.
- Hold any UI-relevant state (sorting, filtering, selection, edit mode).
- Provide read access to the view (the view reads to render).
- Provide mutation operations (the controller calls them in response to user input).

### Non-responsibilities

- Rendering. That is the view's job.
- Input handling. That is the controller's job.
- Business decisions outside the UI. In a larger system, those live in the application core, not in the MVC model.
- Persistence. Saving and loading from a store is the data layer's job (or the application core in hexagonal/clean/onion).

### What the model is, in different contexts

- **In a small UI-only app:** the model may coincide with the domain model. There is one model class per concept.
- **Inside a larger architecture (hexagonal, clean, onion, layered):** the model is a **view-model** or DTO. It is populated from the application core but is not the domain model.

### Variants

- **Active model:** holds state and behaviour, including notifying the view of changes. Common in classical Smalltalk MVC and in some desktop UIs.
- **Passive model (DTO):** just data. Mutation is done by the controller; the view re-renders on demand. Common in web MVC.

## View

The view **renders the model**. It produces visual output (HTML, JSX, native widgets).

### Responsibilities

- Read the model.
- Render the visual representation.
- Display dynamic data (lists, conditionals, formatting).
- Possibly notify the controller of UI events (button clicks, keystrokes, form submissions). The "notify" is typically wiring through the framework, not direct calls.

### Non-responsibilities

- Mutating the model.
- Calling services or the application core.
- Querying data.
- Containing business rules.

### Variants

- **Server-rendered template (Rails, Django, classical PHP):** the view is a template that produces HTML. The controller passes the model into the template.
- **Client-rendered component (React, Vue, Angular):** the view is a component tree that re-renders when the model changes. The component reads from a store or props; it does not mutate.
- **Native widget tree (Swing, WPF):** the view is a hierarchy of UI widgets. The view binds to the model directly or through bindings.

### "Dumb view" rule

In modern MVC, views are dumb. They render. They do not decide. If a view has logic that decides what to show based on rules beyond simple conditionals (e.g. computes a derived value, calls a service, mutates state), that logic belongs in the controller or the model, not in the view.

## Controller

The controller **mediates** between the user and the model. It receives input, decides what to do, calls the model, and selects the view.

### Responsibilities

- Receive user input (HTTP request, GUI event, CLI command in MVC-shaped CLIs).
- Validate the input structurally (presence, format).
- Call the model to read or mutate state.
- In a larger architecture: call the application core (use cases, services) and use the result to populate the view-model.
- Select which view to render and pass the model to it.
- Translate the result back into the UI's protocol (HTTP response, GUI update).

### Non-responsibilities

- Rendering. The view's job.
- Containing business rules (in larger architectures). Those live in the application core.
- Direct data access (in larger architectures). The controller calls the application core, not the database.
- Holding state across requests (in web MVC). Controllers are stateless; state lives in the model.

### "Thin controller" rule

Controllers are thin. Their body reads as a sequence:

1. Validate input.
2. Call the application core (or the model in small apps).
3. Build the view-model (or update the model).
4. Select the view.
5. Return.

If a controller's body is longer than this and does not just decompose into helpers, the controller is doing more than one thing.

## Interaction patterns

Different MVC variants connect the roles differently.

### Classical Smalltalk MVC (active model)

```
View ----observes---->  Model
 ^                       ^
 |                       |
input                  mutates
 |                       |
Controller -------------+
```

The view observes the model. The controller mutates the model. The model notifies observers of changes.

### Web MVC (passive model)

```
HTTP request --> Controller --> Model (read/write) --> View --> HTTP response
```

The controller drives. The model is queried or mutated. The view renders the model into HTML.

### MVC inside a hexagonal driving adapter

```
HTTP request --> Controller --> Application Core (use case) --> [returns result]
                                                                       |
                                                                       v
                                                      View-Model populated
                                                                       |
                                                                       v
                                                      View renders --> HTTP response
```

The controller is the driving adapter. The application core does the business work. The view-model is built from the result. The view renders.

## What the model is NOT (in a larger architecture)

In a hexagonal, clean, onion, or layered system:

- The MVC model is **not** the domain model.
- The MVC model is **not** the persistence row.
- The MVC model is a **DTO or view-model** populated by the controller from the application core's result.
- Confusing the MVC model with the domain model leads to leaking domain types into the view, or shaping the domain after the UI's needs.

## Output

For each MVC piece in the codebase, the team can answer:

- Which role does this class play (model, view, controller)?
- Does it respect the role's responsibilities?
- In a larger architecture, what is the relationship to the domain model and the application core?
- Is the view dumb? Is the controller thin? Is the model UI-side?

Without these answers, the roles drift and MVC degenerates into "code in controllers".
