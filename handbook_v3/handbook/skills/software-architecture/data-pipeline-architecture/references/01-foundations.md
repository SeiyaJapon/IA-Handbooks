# 01. Foundations

Data pipelines move and transform data from source systems to destinations. The category includes ETL, ELT, streaming, ingestion, and materialised view systems.

## What data pipeline architecture solves

- **Decoupling source and destination.** Source systems do not know about destinations.
- **Transformation at scale.** Apply transformations consistently and repeatably.
- **Backfill and reprocessing.** Historical data can be re-derived.
- **Multiple downstream consumers.** Many systems read the same processed data.
- **Operational discipline.** Lag, errors, data quality are observable.

## What it does not solve

- General application logic.
- User-facing operations (use a backend architecture).
- Data ownership decisions (pipelines move data; ownership is decided elsewhere).

## When pipelines apply

- ETL/ELT workflows.
- Streaming ingestion.
- Materialised views and projections.
- Cross-system data integration.
- Reporting and analytics data preparation.

## When pipelines do not apply

- Small ad-hoc data movement (a script suffices).
- Data movement embedded in business logic (it lives in the backend, not in a separate pipeline).
- One-off migrations.

## Modern pipeline shapes

- **Batch ETL.** Schedule-driven, transforms data in bulk.
- **Streaming.** Event-by-event, low latency.
- **Lambda architecture.** Combines batch and streaming for the same data.
- **Kappa architecture.** Stream-only; batch is replay over the stream.
- **Medallion (bronze/silver/gold).** Layered transformations with quality progressively improved.

## Common platforms

- Apache Airflow, Prefect, Dagster (orchestrators).
- Apache Spark, Databricks (compute).
- Apache Kafka, Kinesis (streaming).
- dbt (transformation in the warehouse).
- AWS Glue, Step Functions, EventBridge.

The platform is operational; the architecture is the structural decision.
