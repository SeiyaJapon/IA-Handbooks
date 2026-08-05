# Frontend Architecture Skill

Use this skill when designing, reviewing, or refactoring the architecture of a frontend application: SPA, web client, mobile web, or any browser-rendered UI. Triggers: frontend architecture, component architecture, state management, module boundaries, feature modules, lazy loading, frontend layers.

## What frontend architecture is

Frontend architecture is the **structural decision** about how a UI client is organised: what components exist, how they relate, where state lives, how data flows from APIs to screens, how features are isolated.

It is its own architectural family because:

- The runtime is the browser (or webview, mobile shell), with constraints absent in backends (bundle size, hydration, SSR, accessibility).
- The user is the primary actor, not other systems.
- State management is a first-order concern, often dominating the design.
- Components are the unit of composition, not classes or modules in the backend sense.

## What it is and is not

Frontend architecture is NOT:

- A synonym of MVC, MVP, or MVVM. Those are UI structuring patterns; frontend architecture is the system-level decision.
- A synonym of a framework (React, Vue, Angular, Svelte). Frameworks impose conventions; the architecture is the rules enforced on top.
- The same as backend architecture. Hexagonal/clean/onion can inform frontends but do not transfer directly; the forces are different.
- "Component design". Component design is one part; module organisation, state management, data flow, routing, and bundling are others.

## Non-negotiable rule

Never start frontend work from "what components to build".

The mandatory order is:

1. Identify the **features** (business capabilities the UI serves).
2. Decide the **module boundaries** (one module per feature, or per route group, or per domain capability).
3. Decide where **state** lives (per component, per feature, global).
4. Decide the **data flow** (where API calls happen, where data is cached, how state updates propagate).
5. Decide **routing** (lazy-loaded feature modules, route guards).
6. Decide **shared layers** (UI primitives, design system, utilities).
7. Only then discuss components, hooks, services, and concrete framework features.

## Hard rules

### Feature isolation

Code for one feature lives together. Cross-feature imports go through explicit shared modules, not through reaching into another feature's internals.

### State ownership

State lives at the lowest level where it is needed. Lifting state higher than necessary causes prop drilling and unnecessary re-renders. Lifting too low causes inconsistency.

### Components are composable, not god-objects

A component does one thing: render based on props/state, handle one kind of event, or compose other components. A component with twenty props and a hundred lines of logic is doing too much.

### API calls are not in components

API access is in a service layer, a hook, or a query library. Components consume the result.

### Business rules are not in the frontend

The frontend may have UI logic (when to show a button, how to format a date) but business rules (what is allowed, what is computed, what is valid) live on the backend. The frontend may duplicate them for UX, but the backend is authoritative.

## Forbidden shortcuts

- "The framework is the architecture."
- "Each component is a microservice."
- "Redux is the architecture."
- "We will figure out state later."
- "Business rules in the frontend are fine because the backend also checks."

## Mandatory review behavior

When reviewing a frontend:

1. Are features isolated, with no cross-feature imports?
2. Is state ownership explicit (per component, per feature, global)?
3. Are API calls confined to a service layer or query library, not in components?
4. Are components composable, with single responsibilities?
5. Is routing structured (lazy-loaded feature modules, guards)?
6. Are shared modules genuinely shared, not feature-specific code that leaked?
7. Is the bundle size monitored?

## When this skill applies

- Any non-trivial SPA, web client, or rich UI application.

## When this skill does not apply

- Backend services (use the appropriate backend architecture skill).
- Server-rendered HTML with no client-side state (a layered backend with template rendering is enough).
- Trivial UIs (a single page, a form, a script).

## References

- `references/01-foundations.md` for what frontend architecture is, when it applies.
- `references/02-feature-modules.md` for module boundaries and feature isolation.
- `references/03-state-management.md` for state ownership patterns and trade-offs.
- `references/04-data-flow.md` for API integration, caching, optimistic updates.
- `references/05-routing-and-lazy-loading.md` for navigation, code splitting, route guards.
- `references/06-shared-layers.md` for UI primitives, design system, utilities.
- `references/07-relationship-with-other-disciplines.md` for frontend vs MVC/MVP/MVVM, vs Flux/Redux, vs backend architectures.
- `references/08-anti-patterns.md` for fat components, prop drilling, business in the frontend.
- `references/09-review-checklist.md` for reviewing a frontend codebase.
- `references/10-user-preferences.md` for Francisco's project conventions.
