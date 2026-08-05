# 06. Shared Layers

Frontend code that is genuinely shared lives in dedicated layers. Mixing shared and feature-specific code is the most common cause of frontend codebases drifting into a tangle.

## Typical shared layers

### UI primitives

Buttons, inputs, modals, layout containers. Generic, reusable, not feature-specific.

Lives in `shared/ui/` or `components/ui/` or a separate package (a design system).

Rule: a UI primitive does not know about features. A button is a button; it does not know about scenarios.

### Design system

A more formal version of UI primitives: tokens (colours, spacing, typography), themes, accessibility helpers, animation primitives.

For larger projects, the design system is its own package, versioned separately.

### Utilities

Pure functions: formatters, validators, parsers, type guards, math helpers.

Rule: utilities have no UI dependencies. They are pure logic.

### API client

A shared API client: HTTP client, authentication, base URL, common interceptors.

Feature services build on top of the shared client.

### Cross-cutting types

Types that multiple features use: user, auth, error shapes, common DTOs.

### Cross-cutting state

Auth state, theme state, current user, feature flags. Lives in a global store accessed by features.

## Rule: shared imports features, not the reverse

The dependency direction:

```
features/* → shared/*
shared/* does NOT import features/*
```

If a shared module needs to know about a feature, it is not shared. Move it to the feature.

## Cohesion vs reuse

The trap: extracting too eagerly to shared, before two features actually need the same thing.

Recommendation: keep code in the feature until two or more features genuinely need it. Then extract.

Premature shared extraction creates abstractions that fit no feature exactly.

## Anti-patterns

- **`shared/misc/` or `shared/utils/`.** Dumping ground for code without a clear home. Becomes a tangle.
- **Shared module that imports features.** Inverts the dependency. The shared module is feature-specific in disguise.
- **One giant `shared/` with hundreds of files.** Lost cohesion. Split into focused sub-modules.
- **Premature extraction.** Code extracted "in case" before any feature reuses it. Abstractions that fit no real use.
- **Feature-specific code labelled shared.** "DashboardWidget" in shared. It is not shared; it is feature-specific.

## Output

For each item in the shared layer, the team can answer:

- Is it genuinely used by multiple features?
- Does it know nothing about specific features?
- Is its purpose clear from its location?
