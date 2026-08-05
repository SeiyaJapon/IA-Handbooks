# 07. Review Checklist

## Step 1: Are the patterns in use justified?

For each pattern (CQRS, ES, outbox, saga, ACL, etc.):

1. What problem does it solve in this system?
2. What forces justify it?
3. Was a simpler alternative considered?
4. Is the decision documented somewhere durable?

If a pattern is in use without justification, it is ceremony.

## Step 2: Are patterns applied correctly?

1. CQRS: are read and write models genuinely separate where it matters?
2. ES: are events the source of truth, with schema versioning and snapshots?
3. Outbox: is the relay running, monitored, with backpressure?
4. Saga: state, compensation, observability?
5. ACL: does it translate or just pass through?
6. Repository: per-aggregate, domain-named?

## Step 3: Operational maturity

1. Are the operational concerns of each pattern covered?
2. Are observability dashboards in place?
3. Is the team trained on the patterns in use?

## Step 4: Composition

1. If multiple patterns are in use, do they compose deliberately?
2. Is each pattern individually justified?
3. Or does the combination justify each (a smell)?

## Step 5: Half-applied patterns

1. Are there patterns adopted partially?
2. Is the cost paid without the benefit?

## Step 6: Frozen patterns

1. Are there patterns adopted years ago for forces that no longer exist?
2. Is there a periodic review?

## Summary

Top three findings. Quick wins. Backlog. Confirmed strengths.

## Forbidden conclusions

- "It is fine because we use modern patterns."
- "It is fine because the framework supports the pattern."
- "It is fine because everyone uses CQRS / ES / saga."

The properties are problem fit, correct application, operational maturity, deliberate composition, current justification. Pattern presence alone does not validate it.
