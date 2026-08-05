# 07. Review Checklist

## Step 1: Does serverless apply?

1. Is the workload bursty or event-driven?
2. Does it complete within the platform's time limit?
3. Is cold start latency acceptable?
4. Is vendor lock-in acceptable, or mitigated?

**Stop condition.** Two no's: serverless is the wrong fit. Recommend long-running compute.

## Step 2: Function granularity

1. Does each function have one clear job?
2. Is there a mega-handler that routes internally?
3. Are functions named after business operations, not tables or generic terms?

## Step 3: Handler thinness

1. Is the handler a thin driving adapter?
2. Are business rules in the handler?
3. Does the handler reach a database directly without a port?
4. Is the application core inside the function platform-free (hexagonal/clean/onion)?

## Step 4: Cold starts

1. Are latency-critical paths identified?
2. Are mitigations in place (provisioned concurrency, runtime choice, bundle size)?
3. Is module-level initialisation lean?

## Step 5: State and connections

1. Is state outside the function (database, cache, queue)?
2. Are database connections pooled at module level?
3. Is there a database proxy or connection-less database for high-concurrency functions?
4. Are secrets handled correctly (not in code)?

## Step 6: Workflow and orchestration

1. Are workflows that span multiple functions handled by Step Functions, sagas, or events?
2. Are there synchronous chains of three or more functions?

## Step 7: Observability

1. Structured logs?
2. Distributed tracing?
3. Correlation IDs?
4. Dashboards and alerts?

## Step 8: Vendor coupling

1. Is platform-specific code confined to the handler?
2. Is the core portable to another platform?
3. Are platform features (Lambda layers, EventBridge schema registry) used in a way that locks the project in?

## Summary

After all steps, produce:

- Top three findings.
- Quick wins.
- Backlog.
- Confirmed strengths.

## Forbidden conclusions

- "It is fine because we use Lambda."
- "It is fine because the function works in tests."
- "Cold starts are fine because we have not noticed them."

The properties are workload fit, function design, cold start handling, state and connections, observability, vendor coupling. Platform name and tests alone do not validate serverless.
