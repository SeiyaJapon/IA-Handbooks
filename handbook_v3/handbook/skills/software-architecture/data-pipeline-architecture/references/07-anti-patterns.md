# 07. Anti-patterns

## Non-idempotent stages

Reprocessing duplicates effects. Retries multiply records.

Fix: upsert by key, replace partition wholesale, dedup tables.

## No DLQ

Bad records block the pipeline or are silently lost.

Fix: DLQ at every stage that can fail on bad data. Alert on DLQ growth.

## Silent failures

`try/catch` that swallows errors. The pipeline runs; the data is wrong.

Fix: errors propagate. Failed records to DLQ. Logs and alerts.

## No backfill capability

A bug fix or new logic cannot be applied to historical data.

Fix: idempotent stages + source retention + tooling for triggering backfill.

## Pipeline per script

Hundreds of one-off scripts running on cron. No structure, no recovery, no observability.

Fix: orchestrator (Airflow, Prefect, Step Functions). Stages with checkpoints. Observability dashboards.

## Schema-less

Records as untyped JSON. Downstream parses defensively. Schema changes break consumers.

Fix: documented, versioned schema. Validation at ingest.

## Stages that reach into next stage's state

Stage A writes directly to stage B's working storage. The contract is implicit.

Fix: each stage's output is the next stage's input via a documented contract (table, topic, file).

## Streaming because it is modern

Operational cost not justified. Batch would have been simpler.

Fix: revisit the freshness requirements. Pick the simplest paradigm that fits.

## State that grows without bound

Aggregations or joins without windowing or expiry. Memory or storage exhaustion.

Fix: bound state with windows, retention, or explicit expiry rules.

## Distributed pipeline of synchronous calls

Stages call each other synchronously across services. Latency stacks; failures cascade.

Fix: stages communicate through stable storage (queue, table, file), not synchronous calls.

## No quality dashboard

Consumers discover quality issues; the pipeline owner does not.

Fix: dashboards for record counts, error rates, freshness, schema drift.

## "Airflow is the architecture"

The team adopted Airflow and called it the architecture. No idempotency, no DLQ, no schemas.

Fix: Airflow is the orchestrator. The architecture is the design of stages, idempotency, failure handling, schemas.

## Output

When reviewing a pipeline, look for these patterns. Non-idempotent stages, no DLQ, schema-less, and silent failures are the most common and most damaging.
