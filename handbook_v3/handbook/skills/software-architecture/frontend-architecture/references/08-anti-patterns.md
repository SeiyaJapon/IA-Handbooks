# 08. Anti-patterns

## Fat component

A component with hundreds of lines, many props, internal state, side effects, API calls.

Why it fails: hard to reason about, hard to test, hard to change without breaking. Re-renders are wide.

Fix: split. Extract sub-components. Move logic to hooks. Move API to a service.

## Cross-feature internal imports

Feature A imports `features/B/components/Internal.tsx`.

Why it fails: features are no longer isolated. Changes in B break A.

Fix: extract the shared piece to `shared/`, or expose it via B's `index.ts` as a public API.

## Prop drilling

Data passed through five levels of components, each just forwarding.

Why it fails: refactoring any level affects all. The intermediate components carry props they do not use.

Fix: lift state to the lowest common ancestor that uses it; or use context; or use a query at the consuming component.

## API calls inside components

`useEffect(() => { fetch(...) }, [])` everywhere.

Why it fails: hard to test, hard to cache, hard to invalidate, hard to share.

Fix: service layer or query library.

## Everything in global state

Component-local state, server data, UI toggles, all in Redux.

Why it fails: wide re-renders, complex selectors, brittle migrations.

Fix: state at the lowest level that needs it. Server state in a query library. Component state in components.

## State duplicated

The same fact stored in two places. Eventually they diverge.

Fix: identify the source of truth. The other places derive from it.

## No invalidation

Cached data shown long after it changed. The user refreshes; the data is wrong.

Fix: invalidation strategy (time-based, action-based, query library).

## Business rules in the frontend

Decisions about what is allowed, what is computed, what is valid live in the frontend without backend enforcement.

Why it fails: the frontend is not authoritative. Anyone calling the API directly bypasses the rules.

Fix: business rules live on the backend. The frontend may duplicate them for UX, but the backend is authoritative.

## Eager bundle

The whole app's code in the initial bundle.

Why it fails: slow first paint, large download.

Fix: lazy-load feature modules.

## "Misc" or "common" or "utils" without scope

Catch-all folders. Grow into tangles.

Fix: name folders by purpose. If something genuinely does not belong, the design has a gap.

## Routing logic in components

Components decide where to navigate based on internal logic.

Fix: routing is the routing layer's job. Components emit events or call navigation; the route definition decides where to go.

## URL not the source of truth

State in components diverges from URL. Refresh loses state.

Fix: URL is part of the application state. Filters, pagination, sort, current view live in the URL.

## Framework-as-architecture

"The architecture is React." Framework is the wiring; architecture is the structure on top.

Fix: name the architecture (feature modules, state management strategy, data flow strategy) explicitly.

## Output

When reviewing a frontend, look for these patterns. Fat components, cross-feature imports, prop drilling, API in components, and state duplication are the most common.
