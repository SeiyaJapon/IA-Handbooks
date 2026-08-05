# 03. State Management

State in a frontend lives at three levels.

## Component state

State local to one component (form input, toggle, hover). Lives inside the component.

Use when: the state is consumed only by that component or its immediate children.

Tools: `useState`, signals, local refs, framework-equivalent.

## Feature state

State shared across multiple components within one feature (a list, a filter, a selection). Lives in a feature-scoped store, hook, or context.

Use when: multiple components in the same feature need the same state.

Tools: a context provider, a feature-scoped store (small Redux slice, Zustand, MobX), a custom hook with internal state.

## Global state

State shared across features (current user, theme, feature flags, navigation).

Use when: multiple features genuinely need the same state.

Tools: a global store (Redux, Zustand, signals, context).

## The lifting rule

Lift state to the lowest common ancestor that needs it. Lifting too high causes prop drilling and wide re-renders. Lifting too low causes inconsistency.

## Server state vs client state

A modern distinction:

- **Client state:** UI state (toggles, form values, current view).
- **Server state:** data fetched from the API, cached on the client (lists, details, paginated data).

Server state is often best handled by a query library (TanStack Query, SWR, Apollo) that handles caching, refetching, invalidation. Client state is handled by component state or a store.

Mixing them in one global store is common but often suboptimal: server state has different lifecycle (cache, invalidate, refetch) than client state.

## Anti-patterns

- **Everything in global state.** Even component-local state ends up in Redux. Wide re-renders, complex selectors.
- **Prop drilling.** State passed through many levels of components. Lift higher or use context.
- **State duplication.** The same fact stored in two places. Eventually they diverge.
- **Server state in a generic store.** TanStack Query, SWR, etc. handle this better.
- **Stale state after navigation.** The previous screen's state lingers.
- **No invalidation strategy.** Cached data shown long after it changed on the server.

## Output

For each piece of state, the team can answer:

- Component, feature, or global?
- Server or client state?
- Where is it stored?
- How is it invalidated?
