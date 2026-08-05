# 04. Data Flow

Data moves between API and UI through a defined path. Letting any component reach the API directly produces tangled state and untestable code.

## The service layer

API calls live in a service layer (or query library). Components do not call `fetch` or `axios` directly.

Service layer responsibilities:

- Construct the API request (method, URL, headers, body).
- Handle authentication.
- Translate API response to typed domain shapes.
- Translate API errors to typed errors.

Components consume the service layer through hooks, queries, or props.

## Query libraries

Modern frontends often use a query library for server state:

- TanStack Query, SWR for React.
- Apollo for GraphQL.
- Pinia/Vuex queries for Vue.

Benefits: caching, automatic refetching, invalidation, retry, deduplication, optimistic updates. The query library is part of the data flow architecture.

## Fetching patterns

- **On mount.** The component fetches when it appears.
- **On route enter.** Route resolvers / loaders fetch before rendering.
- **On user action.** Click triggers a mutation.
- **On schedule.** Polling or refetch intervals.

The pattern depends on the data's freshness requirements.

## Mutations

When the UI changes server state:

- The mutation is triggered by user action.
- The service layer calls the API.
- On success, the cache is updated (optimistic or after response).
- On failure, the UI rolls back and shows an error.

Optimistic updates make the UI feel fast; they require careful rollback on failure.

## Error handling

Three places errors can be handled:

- **Globally.** A central error handler shows toasts or redirects on auth errors.
- **Per query.** The query library reports errors per request; components show inline error states.
- **Per mutation.** Mutations show feedback to the user (toast, inline error).

A frontend without explicit error handling shows broken UIs on failures.

## Loading states

Three loading states matter:

- **Initial load.** No data yet; show skeleton or spinner.
- **Refetch.** Data is stale; show subtle indicator while refetching.
- **Mutation in flight.** Show disabled button or progress indicator.

Conflating them shows a spinner where stale data would be better.

## Anti-patterns

- **API calls inside components.** Direct `fetch` in `useEffect` everywhere. Hard to test, hard to cache.
- **Manual cache management.** Reinventing what query libraries provide.
- **No invalidation.** Cached data shown long after it changed.
- **No error handling.** Errors silently log; UI breaks.
- **Synchronous expectations on async data.** Components access `data.foo` before `data` exists.
- **Prop drilling for data.** Data fetched at the top, passed through ten levels. Use a query at the consuming component instead.

## Output

For each data dependency in the UI, the team can answer:

- Where is the API call?
- How is the response cached?
- When is it invalidated?
- How are errors handled?
- How are loading states represented?
