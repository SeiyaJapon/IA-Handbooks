# 02. Feature Modules

A feature module groups everything related to one feature: components, state, services, routes, types.

## Goals

- **Co-location.** Code for one feature lives together. Easy to find, easy to delete.
- **Isolation.** Features do not import each other's internals. Cross-feature interaction goes through explicit shared modules or events.
- **Independent evolution.** A team owning a feature can change it without breaking others.

## Granularity

A feature is typically a coherent business capability ("dashboard", "scenario simulator", "reports") or a major route group. Not a component (too small), not the whole app (too large).

For larger systems, features may nest: a `scenarios/` feature with sub-features `creation/`, `evaluation/`, `comparison/`.

## Structure inside a feature

```
features/scenarios/
├── components/         (UI components specific to this feature)
├── hooks/              (or composables, or whatever the framework calls them)
├── services/           (API integration specific to this feature)
├── state/              (feature-local state if any)
├── types/              (feature-specific types)
├── routes.ts           (feature's route definitions)
└── index.ts            (the feature's public API)
```

The `index.ts` exports the feature's public surface (what other features can use). Everything not exported is private to the feature.

## Cross-feature interaction

Two features need to interact. Options:

- **Shared module.** Common code (a shared API client, a shared type) lives in a `shared/` module that both features import.
- **Events / state events.** Feature A publishes an event; feature B reacts. The event is a contract.
- **Routing.** Feature A navigates to feature B's route. The URL is the contract.

What is forbidden:

- Feature A imports `features/B/components/SomeInternal.tsx`. This is a cross-feature internal import. Move the shared piece to `shared/`, or expose it via the feature's `index.ts`.
- Feature A reads feature B's internal state directly.

## Shared modules

`shared/` (or `common/`, `core/`) contains:

- UI primitives (buttons, inputs, layout components).
- Utility functions (formatters, validators).
- API client base.
- Cross-cutting types.

Rule: shared modules are imported by features, not the reverse. A shared module that imports a feature is a sign that the code does not belong in shared.

## Anti-patterns

- **Cross-feature internal imports.** Breaks isolation.
- **Shared module that imports features.** Inverts the dependency; shared becomes a god module.
- **Feature without clear scope.** "Misc", "common", "utils" as feature names. Misc grows; misc becomes a tangle.
- **Components in shared that are feature-specific.** A "dashboard widget" in shared is dashboard-specific; move it to features/dashboard.
- **No feature module structure.** All components in a flat folder. Hard to navigate, hard to delete.

## Output

For each feature, the team can answer:

- What is its scope?
- What is its public API?
- What does it import from shared?
- Does it import internals from other features?
