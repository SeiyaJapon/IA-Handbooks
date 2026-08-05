# 06. Relationship with Other Disciplines

## Pipelines vs Backend services

Different purposes.

- A backend service serves user requests, owns business logic and state, and lives long-running.
- A pipeline moves and transforms data, with idempotent stages, scheduled or stream-driven, owns no business logic in the operational sense.

Some systems mix: a backend service that has a small embedded pipeline (export to a warehouse). Larger pipelines should be separate.

## Pipelines vs EDA

Related but distinct.

- EDA is a communication paradigm. Components communicate via events.
- A streaming pipeline consumes and produces events. The pipeline is one consumer/producer in an EDA system.
- Batch pipelines do not require EDA; they read from snapshots.

## Pipelines vs Microservices

A pipeline can be a microservice (a service whose job is data movement). Or it can be a separate platform (Airflow, Spark) outside the microservices.

## Pipelines vs Hexagonal / Clean / Onion

Composable internally. A pipeline stage's logic can be hexagonal: a port for the source, a port for the sink, business logic in the middle. For complex pipelines, this isolation helps testability.

For simple pipelines (a SQL transformation, a Spark job), the architecture is the pipeline itself; internal hexagonal layering is overkill.

## Pipelines vs Event Sourcing

Event Sourcing produces an event log; a pipeline can consume it. The pipeline does not own the event source; it derives from it.

## Pipelines vs Serverless

Pipelines can be implemented as serverless functions (one Lambda per stage) or as long-running platforms (Spark, Flink). Choice depends on workload.

## Summary

| Discipline | Type | Relationship |
|---|---|---|
| Backend services | System architecture | Different purpose; some overlap. |
| EDA | Communication paradigm | Composable; streaming pipelines are EDA consumers/producers. |
| Microservices | Deployment | A pipeline can be one or several microservices. |
| Hexagonal / Clean / Onion | System architecture | Composable internally for complex stages. |
| Event Sourcing | Persistence pattern | Pipelines consume event sources. |
| Serverless | Runtime | Pipelines can be serverless. |
