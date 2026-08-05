# 04. Variants

The MVC name is used for many similar but distinct patterns. This document distinguishes the main variants.

## Classical Smalltalk MVC (Reenskaug, 1979)

The original. The view observes the model; the controller updates the model; the model notifies observers.

Characteristics:

- Model is **active**: holds state and notifies observers.
- View **observes** the model directly.
- Controller updates the model in response to input.
- The view-controller-model triplet exists per UI element (a button has its own MVC).

Where it appears: classical desktop UIs, especially in Smalltalk and some Cocoa code.

Today: rare in pure form. The "active model + observers" pattern survives in reactive frameworks, but the strict triplet-per-widget is not common.

## Web MVC

The mainstream interpretation in 1990s-2000s web frameworks (Rails, Django, ASP.NET MVC, classical PHP).

Characteristics:

- Model is **passive**: data plus methods to load and save.
- View is a **template**: produces HTML.
- Controller is the **request handler**: receives HTTP, calls the model, picks a view, returns rendered HTML.

The flow is request-driven, not observer-driven. The view does not observe the model; it is rendered once per request.

Where it appears: classical Rails, classical Django, classical PHP frameworks, ASP.NET MVC. Many modern frameworks still use this shape.

## MVC inside a hexagonal/clean/onion driving adapter

The modern composition for backend systems. MVC structures the HTTP driving adapter; the application core is outside MVC.

Characteristics:

- Controller is the driving adapter.
- Model is a view-model / DTO populated from the application core.
- View is the rendering (HTML template, JSON serialisation, JSX component).

Where it appears: most current backend systems that use MVC vocabulary today.

## MVP (Model-View-Presenter)

A variant where the view is **passive** and the **presenter** mediates.

Characteristics:

- Model is data.
- View is passive: it exposes interfaces (set this label, show this list) but does not decide.
- Presenter holds the logic: receives input from the view, calls the model, updates the view.

Difference from MVC:

- The view does not observe the model directly.
- The presenter is more involved than the MVC controller.

Where it appears: some desktop UIs, mobile (older Android, iOS patterns), enterprise UIs with rigid testing requirements.

## MVVM (Model-View-ViewModel)

A variant where the view binds to a view-model and the view-model communicates with the model.

Characteristics:

- Model is data.
- ViewModel exposes properties and commands.
- View binds to the ViewModel declaratively.
- Bindings keep the view in sync with the ViewModel.

Difference from MVC:

- No explicit controller. The bindings replace the controller's mediation.
- The ViewModel is similar in spirit to the MVP presenter, but with declarative bindings instead of imperative method calls.

Where it appears: WPF, Silverlight, many modern frontend frameworks (Vue, Knockout, parts of Angular), some XAML platforms.

## Flux / Redux / Elm-style architectures

Sometimes called MVC-like, but they are different patterns:

- Unidirectional data flow.
- Single store / single state.
- Actions and reducers replace controllers and model mutations.

These are not MVC variants in the traditional sense. They are their own architectures for frontend state. This handbook does not have a dedicated skill for them yet; if a project uses them, treat them as a separate decision.

## How to tell them apart

Quick disambiguation:

| Pattern | Active model? | Observer? | Controller / Presenter / ViewModel? |
|---|---|---|---|
| Classical MVC | Yes | View observes Model | Controller |
| Web MVC | Passive | No (request-driven) | Controller |
| MVC inside hexagonal | Passive (view-model) | No | Controller (driving adapter) |
| MVP | Passive (sometimes active) | Sometimes | Presenter |
| MVVM | Active or reactive | Bindings | ViewModel |
| Flux/Redux | Single store | Subscriptions | Actions/Reducers (not controllers) |

## Which to use

- **Backend with MVC inside a driving adapter:** the modern composition (MVC inside hexagonal/clean/onion/layered). Default for backends today.
- **Classical desktop UI:** classical MVC, MVP, or MVVM, depending on the platform's conventions.
- **Modern frontend:** the framework chooses (React + Redux, Vue with Composition API, Angular). MVC vocabulary is loose here; do not force it.

## Output

When MVC vocabulary is in use, the team can answer:

- Which variant of MVC are we using?
- Why this variant?
- Are we using MVC because the platform expects it, or because the forces justify it?

Without these, MVC becomes "we have controllers", which is not a meaningful architectural statement.
