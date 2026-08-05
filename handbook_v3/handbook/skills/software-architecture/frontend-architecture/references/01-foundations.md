# 01. Foundations

Frontend architecture concerns the structure of UI applications running in the browser, mobile web, or webview. It evolved alongside frameworks (React 2013, Angular 2010, Vue 2014) and from earlier client-side architectures (Backbone, Knockout).

## What frontend architecture solves

- Organising features so they can evolve independently.
- Managing state across many components and routes.
- Coordinating data flow between API and UI.
- Keeping bundle size manageable.
- Isolating shared UI primitives from feature-specific code.

## What it does not solve

- Backend concerns (data ownership, persistence, business rules).
- Performance below the browser level (network, server response time).
- UX or visual design.

## When it applies

- Any non-trivial SPA, web client, or rich UI application.
- Mobile web apps.
- Webview-hosted UIs in mobile or desktop.

## When it does not apply

- Server-rendered HTML with no client state.
- Trivial pages.
- Backend systems (different forces).

## Core decisions

A frontend architecture answers:

1. How are features modularised?
2. Where does state live?
3. How does data flow from API to screen?
4. How is the application routed?
5. What is shared, and how?
6. What is the bundle strategy?

The framework imposes some defaults; the architecture is the rules enforced on top.
