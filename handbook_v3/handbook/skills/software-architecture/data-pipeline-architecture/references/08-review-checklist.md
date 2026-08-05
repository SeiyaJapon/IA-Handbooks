# 08. Review Checklist

## Step 1: Does this need a pipeline architecture?

1. Is the system's primary purpose data movement and transformation?
2. Or is it a backend service with incidental data movement?

If the latter, the right architecture is a backend architecture (hexagonal, layered) with the data movement as an internal concern.

## Step 2: Stages

1. Are the stages identified and named?
2. Does each stage have one responsibility?
3. Are stages testable in isolation?
4. Are stage inputs and outputs documented?

## Step 3: Idempotency

1. Is each stage idempotent?
2. What pattern provides idempotency (upsert, partition replace, dedup)?
3. Can a stage be safely retried?

## Step 4: State and checkpointing

1. Do stages with state checkpoint?
2. Is state bounded?
3. Is recovery from a failure tested?

## Step 5: Failure handling

1. Retry strategy?
2. DLQ at every stage that can fail on bad data?
3. Alerting on DLQ growth?

## Step 6: Backfill

1. Is backfill a known operation?
2. Is it tested?
3. Is source retention sufficient?

## Step 7: Schema and quality

1. Schemas documented and versioned?
2. Validation at ingest and at key stages?
3. Quality dashboard in place?

## Step 8: Batch vs streaming choice

1. Is the choice justified by freshness requirements?
2. Are operational costs of streaming acceptable for this team?

## Step 9: Observability

1. Lag visible?
2. Error rates per stage?
3. Data quality metrics?
4. Alerts configured?

## Summary

Top three findings. Quick wins. Backlog. Confirmed strengths.

## Forbidden conclusions

- "It is fine because Airflow / Spark / Databricks runs it."
- "It is fine because data flows."
- "It is fine because the warehouse has data."

The properties are stages, idempotency, schema, failure handling, backfill, observability. Platform and presence of data alone do not validate the pipeline.
