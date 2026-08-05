# 09. User Preferences (Francisco)

Project conventions for data pipelines in Francisco Pérez's projects at Kintai.

## Default platforms

- **Databricks** for batch and streaming compute on the data lake.
- **dbt** for transformations in the warehouse where SQL fits.
- **AWS Glue / EventBridge / SQS** for AWS-native pipelines.
- **Step Functions** for orchestration of cross-service workflows.

## Layered model

The medallion model (bronze / silver / gold) is the default for data lake pipelines:

- **Bronze:** raw data, minimal validation.
- **Silver:** cleaned, schema-enforced.
- **Gold:** business-ready, aggregated for consumers.

Consumers read gold; bronze and silver are operational.

## Idempotency

- Upsert by key for transactional data.
- Partition replace for time-partitioned data (overwrite the day's partition wholesale on rerun).
- Dedup tables (inbox pattern) for streaming ingestion.

Non-idempotent stages are flagged immediately.

## Failure handling

- Every stage with input has retry with exponential backoff and bounded count.
- Every stage with input has a DLQ (or dead-letter table).
- Alerts on DLQ growth.
- Alerts on pipeline lag exceeding threshold.

## Backfill

- Each pipeline has a tested backfill operation.
- Source retention is sufficient for the typical backfill range (rolling window, reviewed periodically).

## Schema

- Schemas are documented (registry, repo, dbt schema, or explicit doc).
- Schema changes follow a versioning strategy. Breaking changes go through a migration window.

## Observability

- Lag dashboards.
- Error rates per stage.
- DLQ growth.
- Data quality dashboards (Great Expectations, dbt tests, custom).
- Alerts configured before production.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the project standardises on dbt for warehouse transformations or uses Spark/Databricks for all transforms.
- Whether DLQ destinations are S3, dedicated tables, or a shared dead-letter system.
- Whether streaming pipelines are Kafka-based, Kinesis-based, or platform-mixed.
- Whether quality checks are Great Expectations, dbt tests, or custom.

These are open. Do not assume; ask.
