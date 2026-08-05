# 05. Routing and Lazy Loading

Routing is the structural backbone of the frontend: which feature renders for which URL, how navigation works, what is loaded when.

## Route structure

Routes mirror the feature structure:

```
/                     → home
/scenarios            → scenarios list (features/scenarios)
/scenarios/new        → scenario creation
/scenarios/:id        → scenario detail
/reports              → reports (features/reports)
```

Each top-level feature owns its routes. Feature-internal routes are defined within the feature module.

## Lazy loading

Each feature module is a lazy-loaded chunk. The feature's code is downloaded only when its route is visited.

Benefits:

- Smaller initial bundle.
- Faster first paint.
- Features evolve without affecting the bundle of others.

Tools: framework-native (React.lazy + Suspense, Vue async components, Angular loadChildren), bundler-native (webpack code splitting, Vite dynamic imports).

## Route guards

Some routes require authentication, permissions, or pre-loading. Route guards run before the route activates:

- Auth guard: redirect to login if not authenticated.
- Permission guard: redirect to forbidden if missing role.
- Resolver / loader: fetch data before rendering.

Guards live with the routing layer, not inside components.

## URL as state

The URL is part of the application's state. Bookmarkable, shareable, refreshable.

What belongs in the URL:

- Current view (route).
- Major filters and tabs (if shareable).
- Pagination cursor or page number.
- Sort order.

What does not belong in the URL:

- Form input values (until submitted).
- Transient UI state (open dropdowns, hover state).
- Sensitive data.

## Deep linking

Every meaningful state should have a URL. A user must be able to bookmark a filtered view and return later to the same view.

## Anti-patterns

- **Eager loading the entire app.** First paint takes seconds because the bundle has every feature.
- **Routing logic inside components.** Components decide where to navigate based on internal state. The routing layer should be the single source of route changes.
- **No guards.** Authentication checks scattered across components; missed in some.
- **State in components instead of URL.** Filters stored only in component state; refresh loses them.
- **URL not the source of truth.** Component state and URL diverge.

## Output

For each route, the team can answer:

- Which feature does it belong to?
- Is it lazy-loaded?
- What guards run before it?
- What state is encoded in the URL?
