# 10. User Preferences (Francisco)

Project conventions for frontend architecture in Francisco Pérez's projects at Kintai.

## Default framework

The Kintai frontend uses **Angular (standalone components)**. Other frontends in this project default to React unless a forces argument justifies otherwise.

## Feature modules

- One feature per business capability or major route group.
- Feature directory contains its components, hooks/services, state, types, and routes.
- Public API of a feature is exposed via an `index.ts` (or framework-equivalent).
- Cross-feature internal imports are forbidden.

## State management

- Component-local state for transient UI (form inputs, toggles).
- Feature-local state for state used across one feature's components.
- Global state only for genuinely cross-cutting concerns (auth, theme, current user, feature flags).
- Server state is handled by a query library (in React: TanStack Query; in Angular: signals + RxJS or a dedicated library).

## Data flow

- API calls are in a service layer or query library. Components do not call `fetch`/`axios` directly.
- Errors are handled at three levels: globally (auth, network), per query (component), per mutation (toast / inline).
- Loading states are explicit (skeleton, spinner, refetch indicator).

## Business rules

Business rules are NOT in the frontend.

- The frontend may duplicate rules for UX (disable a button when a value is invalid), but the backend is authoritative.
- Sensitive checks (authorisation, eligibility) live on the backend only.

## Routing

- Routes mirror feature structure.
- Feature modules are lazy-loaded.
- Auth and permission guards run before route activation.
- URL is the source of truth for navigation state (filters, pagination, sort, current view).

## Shared layers

- `shared/ui/`: UI primitives (buttons, inputs, layout).
- `shared/utils/`: pure utilities (formatters, validators).
- `shared/api/`: shared API client base.
- `shared/types/`: cross-cutting types.
- `shared/state/`: cross-cutting global state.

Shared imports features → forbidden.

## Bundle

- Initial bundle size monitored.
- Feature modules lazy-loaded.
- Heavy dependencies (charts, editors) lazy-loaded.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the canonical state library in React projects is TanStack Query + Zustand, TanStack Query + context, or another combination.
- Whether the Angular frontend uses signals, RxJS, or a hybrid.
- Whether feature modules are pure folders or formal Angular modules / framework modules.
- Whether design tokens are managed centrally (a design system package) or per project.

These are open. Do not assume; ask.
