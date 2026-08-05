# 04. Failure Handling

Pipelines fail. The architecture decides what happens when they do.

## Failure modes

- **Source unavailable.** The producer cannot read input.
- **Transform error.** A bug or unexpected data crashes a stage.
- **Bad data.** A record violates expectations.
- **Destination unavailable.** The sink cannot write.
- **Resource exhaustion.** Out of memory, out of disk, broker full.
- **Timeout.** A stage takes too long.

## Retry

For transient failures (network, broker briefly down), retry with backoff.

- Bounded retry count.
- Exponential backoff with jitter.
- Per-stage configuration.

What not to retry: deterministic failures (bad data, schema mismatch). Retrying does not help.

## Dead-letter queue (DLQ) / dead-letter table

Records that cannot be processed after retries go to a DLQ. The DLQ:

- Preserves the bad record with enough context to debug (source, stage, error message, timestamp).
- Allows manual inspection and reprocessing after the bug is fixed.
- Triggers alerting when growing.

A pipeline without a DLQ either loses data or blocks on bad records.

## Circuit breaker

When a downstream system is failing repeatedly, stop calling it for a period. Avoids hammering a struggling system and cascading failure.

## Backfill

Reprocessing historical data after a bug fix or new logic.

Requirements:

- Idempotent stages (so reprocessing produces correct results).
- Source retention long enough (events, files, snapshots).
- Operational tooling (a way to trigger backfill for a date range).

## Recovery

After a pipeline crashes, it must resume without:

- Losing data (events not processed).
- Duplicating data (events processed twice).

Achieved through checkpointing + idempotency.

## Alerting

Failures must be visible:

- DLQ growth.
- Lag exceeding threshold.
- Pipeline not running on schedule.
- Data quality checks failing.

A pipeline with logs but no alerts is an outage waiting to be discovered.

## Anti-patterns

- **No retry.** Transient failures cause the whole pipeline to fail.
- **Infinite retry.** Bad records cause infinite loops.
- **No DLQ.** Bad records block the pipeline or are lost.
- **Silent failures.** `try/catch` that swallows errors.
- **Backfill via manual SQL.** Each backfill is a one-off, untested.
- **No alerting.** Pipeline broken; nobody notices.

## Output

For each pipeline, the team can answer:

- What retry strategy applies?
- Where do bad records go?
- How is backfill performed?
- How is the pipeline alerted on?
