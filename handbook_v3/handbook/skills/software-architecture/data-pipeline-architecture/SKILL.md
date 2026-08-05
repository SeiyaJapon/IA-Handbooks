# Data Pipeline Architecture Skill

Use this skill when designing, reviewing, or operating data pipelines: ETL flows, batch jobs, streaming pipelines, data ingestion, transformation chains, materialised views.

## What data pipeline architecture is

A data pipeline architecture is a system whose primary purpose is to **move and transform data**, typically from source systems to a destination (data warehouse, data lake, downstream services).

Defining properties:

- **Stages.** Data flows through a sequence of transformations.
- **Idempotency at each stage.** Reprocessing produces the same result.
- **Backfill capability.** Historical data can be reprocessed.
- **Observability.** Pipeline state, lag, errors are visible.
- **Failure handling.** Failures at any stage are recoverable.

## What it is and is not

Data pipeline architecture is NOT:

- A general-purpose backend architecture. It is for data movement, not for business operations.
- A synonym of ETL. ETL is one shape; pipelines also include streaming, ELT, real-time.
- A database. Pipelines move data between systems; they do not own the destination.

## Non-negotiable rule

Never start pipeline work from "let's use Airflow / Spark / Databricks".

The mandatory order is:

1. Identify the **data sources**: where does data come from, in what shape, at what rate?
2. Identify the **data destination**: where does it go, in what shape, with what guarantees?
3. Identify the **transformations** needed.
4. Decide **batch vs streaming** based on freshness requirements.
5. Decide **idempotency strategy** for each stage.
6. Decide **failure handling** (retry, dead-letter, alerting).
7. Decide **backfill strategy**.
8. Decide **observability** (lag, errors, data quality).
9. Only then discuss specific platforms and frameworks.

## Hard rules

### Each stage is idempotent

Reprocessing a stage produces the same result. Required for retries, backfills, and recovery from failures.

### State is checkpointed

Long-running pipelines persist state at each stage. A failure does not require restarting from scratch.

### Errors do not silently disappear

Failed records go to a dead-letter location with enough context to debug. Logs without alerts are not enough.

### Schema is documented and versioned

Source and destination schemas are explicit. Schema changes follow a versioning strategy.

### Backfill is a first-class concern

The pipeline can reprocess historical data without manual intervention beyond triggering.

## Forbidden shortcuts

- "Data pipeline is just SQL queries on a schedule."
- "We will figure out idempotency later."
- "Errors go to logs; the team will find them."
- "Schema is whatever the source happens to send."
- "Backfill is a one-off script we run when needed."

## Mandatory review behavior

When reviewing a pipeline:

1. Are stages identified and idempotent?
2. Is state checkpointed?
3. Are errors handled (DLQ, retries, alerting)?
4. Is the schema documented and versioned?
5. Is backfill a known, tested operation?
6. Is the pipeline observable (lag, errors, data quality)?
7. Are batch and streaming choices justified?

## When to pick this architecture

- The system's primary purpose is data movement and transformation.
- Multiple data sources feed multiple destinations.
- ETL or ELT workflows are the dominant workload.

## When NOT to pick this architecture

- The system primarily serves user requests (use a backend architecture).
- Data movement is incidental, not the main purpose.
- The volume is small and ad-hoc scripts suffice.

## References

- `references/01-foundations.md` for what data pipelines are, when they apply.
- `references/02-batch-vs-streaming.md` for the two main paradigms.
- `references/03-stages-and-idempotency.md` for stage design and idempotent transformations.
- `references/04-failure-handling.md` for retries, DLQs, recovery, backfill.
- `references/05-data-quality-and-schema.md` for validation, schema management.
- `references/06-relationship-with-other-disciplines.md` for pipelines vs EDA, vs microservices, vs hexagonal.
- `references/07-anti-patterns.md` for common failures.
- `references/08-review-checklist.md` for reviewing pipelines.
- `references/09-user-preferences.md` for Francisco's project conventions.
