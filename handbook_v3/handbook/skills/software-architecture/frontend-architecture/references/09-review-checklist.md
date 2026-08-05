# 09. Review Checklist

## Step 1: Does frontend architecture apply?

1. Is this a non-trivial UI application (SPA, rich client)?
2. Does the project value structural decisions beyond framework defaults?

If no, the framework's defaults may be enough.

## Step 2: Feature isolation

1. Are features grouped in dedicated modules?
2. Do features import from each other's internals?
3. Do features expose a public API (e.g. an `index.ts`)?

## Step 3: State management

1. Is state ownership explicit at each level (component, feature, global)?
2. Is state duplicated anywhere?
3. Is server state separate from client state?
4. Is there an invalidation strategy?

## Step 4: Data flow

1. Are API calls confined to a service layer or query library?
2. Are loading and error states handled?
3. Are mutations explicit, with cache invalidation?

## Step 5: Routing

1. Are routes structured per feature?
2. Are feature modules lazy-loaded?
3. Are guards in place where needed (auth, permissions)?
4. Is the URL the source of truth for navigation state?

## Step 6: Shared layers

1. Are shared modules used by multiple features?
2. Do shared modules import from features (forbidden)?
3. Are there "misc" or "common" dumping grounds?

## Step 7: Bundle

1. Is the bundle size monitored?
2. Are heavy dependencies tree-shaken or lazy-loaded?
3. Is the initial bundle small enough for the target audience?

## Step 8: Component design

1. Are components small and single-responsibility?
2. Are there fat components?
3. Is logic separated from presentation (hooks, services)?

## Summary

Top three findings. Quick wins. Backlog. Confirmed strengths.

## Forbidden conclusions

- "It is fine because we use React/Vue/Angular."
- "It is fine because Redux holds the state."
- "It is fine because the components render."

The properties are feature isolation, state ownership, data flow, routing, shared layers, bundle. Framework, store, and rendering alone do not validate the architecture.
