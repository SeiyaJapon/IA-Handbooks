# Data Pipeline

## Purpose

Review data pipeline design, ETL flows, batch jobs, and data processing patterns for correctness, reliability, and operational soundness.

## Responsibilities

- Review pipeline topology and data flow end to end
- Assess error handling, retries, and dead-letter handling at each step
- Evaluate idempotency of processing steps
- Review data transformation logic for correctness
- Assess state management and checkpointing strategy
- Evaluate monitoring and alerting coverage
- Review data quality checks and validation
- Assess backfill and reprocessing strategy
- Evaluate partial failure handling in batch steps

## Instructions

- Start from the pipeline trigger and trace data through each transformation to the output
- Check each step for idempotency — can it be safely replayed?
- Check error handling: what happens when a step fails partially or fully?
- Check retry logic: is it bounded? is there exponential backoff?
- Check for silent failure — catch blocks that swallow errors without logging or alerting
- Check observability: is there a way to detect if the pipeline is stuck, slow, or failing?
- Check the backfill path: can historical data be reprocessed safely?
- Check data validation: are schema or value assumptions checked before processing?

## Heuristics

Treat as stronger concerns when:

- Non-idempotent steps without deduplication or at-least-once delivery handling
- No dead-letter queue or error sink for failed records
- Silent failure (empty catch block, log-and-continue without alerting)
- Unbounded retry without backoff or maximum attempts
- No monitoring on pipeline completion, latency, or record count
- State stored in memory across steps in a distributed or multi-invocation context
- Data migrations mixed with pipeline processing logic
- Schema assumptions without validation — pipeline breaks silently on unexpected input

Treat as acceptable when:

- Simple linear pipeline without checkpointing if the full run is fast and restartable end to end
- Missing deduplication logic when the source guarantees exactly-once delivery

## Rules

- Idempotency is the default assumption for every pipeline step
- Silent failure is never acceptable — every error path must log and alert
- Flag any step that cannot be replayed safely

## Activity Traceability

🔧 Loading skill: `data-pipeline`
