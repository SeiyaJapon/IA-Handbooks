# 01. Foundations

## Origin

MVC (Model-View-Controller) was introduced by Trygve Reenskaug in 1979 at Xerox PARC, for Smalltalk-80. The original goal was to separate the user's mental model of the data, the visual representation, and the input handling, so that the same data could be shown in different ways and edited through different controls without entangling the three concerns.

The pattern has been reinterpreted many times since: in desktop GUIs (1980s-1990s), in web frameworks (1990s-2000s with classical PHP, Rails, Django, ASP.NET MVC), in mobile and modern frontends (with related variants MVP, MVVM, Flux/Redux, etc.).

The name MVC today refers loosely to "any pattern with model, view, controller pieces". This skill uses MVC in the **modern web/UI sense**: a structural pattern for the presentation side of a system.

## What MVC solves

Without MVC (or a similar separation), UI code tends to mix:

- Reading and writing data.
- Computing what to show.
- Rendering the visual output.
- Handling user input.

The result is monolithic UI files where small changes ripple unpredictably. MVC separates these concerns into three roles, each with a focused responsibility.

## What MVC does not solve

- **System-wide architecture for a backend with business logic.** MVC structures the presentation side; it does not structure the rest of the system.
- **Domain modelling.** The MVC model is a view-side representation; it is not a domain model in the DDD sense.
- **Persistence.** MVC says nothing about how data is stored. The data layer is outside MVC.
- **Inter-context communication.** MVC is local to one UI; it does not address how separate contexts or services talk.

## When MVC applies

MVC applies in two contexts today:

### As a UI structure inside a larger architecture

When the larger system is hexagonal, clean, onion, or layered, MVC may live inside the presentation side or the driving HTTP adapter:

- The HTTP controller plays the MVC controller role.
- The DTO returned to the client plays the MVC model role.
- The template (HTML, JSX, native widget tree) plays the MVC view role.

This is the most common modern use of MVC.

### As the system-wide architecture of small UI-heavy apps

When the application is small, UI-centric, and has little business logic outside the UI, MVC can be the whole architecture. Examples:

- A small desktop tool (a calculator, a configuration editor).
- A simple admin UI over a small data set.
- A prototype.

In these cases, "the architecture is MVC" is honest.

## When MVC does not apply

- As the architecture of a backend service. The business logic does not belong in MVC.
- For long-lived rich-domain systems. The MVC model is view-centric; rich domains need a separate model.
- For systems that are predominantly asynchronous workflows or batch processing.
- As a substitute for thinking about the system's architecture.

## How to decide

Ask in order:

1. Is the question about UI structure?
2. Is the larger architecture already decided (hexagonal, clean, onion, layered, none-because-it-is-a-small-app)?
3. Where does business logic live (in MVC, or outside MVC)?

If business logic lives outside MVC, then MVC is a UI structure inside that larger architecture. If business logic lives in MVC because there isn't much of it and the app is small, MVC may be the whole architecture.

If business logic ends up in MVC by accident in a larger system, that is an anti-pattern (see `05-anti-patterns.md`).

## Common misreadings

- **"MVC is what frameworks like Rails or NestJS do."** Frameworks may use MVC vocabulary, but the architecture is whatever rules the team enforces. A framework with controllers does not automatically make the project MVC in any meaningful sense.
- **"MVC and MVP and MVVM are the same."** They share the spirit (separation of UI roles) but differ in how the roles communicate. They are not interchangeable.
- **"The MVC model is the domain model."** No. The MVC model is the view-side representation. In a small app it may coincide with the domain model; in a richer system it is a separate view-model.
- **"MVC is for web only."** No. MVC originated in desktop and is used across many UI environments.

## What to do when MVC does not apply

- For backend services with business logic: pick a backend architecture (hexagonal, clean, onion, layered). MVC may still live inside the HTTP adapter, but the architecture is not MVC.
- For complex frontends: consider variants (MVP, MVVM) or modern frontend architectures (component-based, Flux/Redux, signal-based).

## Output

When MVC applies, the team can answer:

- Is MVC a UI structure inside a larger architecture, or is it the system-wide architecture of a small UI app?
- Where does business logic live, if any?
- What is the model in this MVC: a domain model, a view-model, a DTO?

Without these answers, MVC tends to absorb business logic and become indistinguishable from "putting code in controllers".
