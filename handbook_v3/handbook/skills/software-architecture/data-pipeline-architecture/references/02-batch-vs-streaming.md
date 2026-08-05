# 02. Batch vs Streaming

Two paradigms for data pipelines.

## Batch

Schedule-driven. Process data in bulk at intervals (hourly, daily, on demand).

Pros:

- Simpler to reason about.
- Easier to recover (rerun a failed batch).
- Natural fit for snapshot-based systems (data warehouses).
- Cheaper for large historical reprocessing.

Cons:

- Latency between data arrival and availability (the schedule interval).
- Spiky resource usage at run time.

Use when:

- Freshness is measured in hours or days.
- Data warehouse / lake destinations.
- Reprocessing is frequent.

## Streaming

Event-by-event. Process each record as it arrives.

Pros:

- Low latency (seconds or less).
- Steady resource usage.
- Natural fit for real-time use cases.

Cons:

- Harder to reason about (state, ordering, exactly-once).
- Recovery is more complex (where to resume from).
- More operational overhead.

Use when:

- Freshness is measured in seconds or sub-second.
- Real-time dashboards, alerts, fraud detection.
- Event-driven destinations.

## Lambda architecture

Combines batch and streaming. Streaming handles real-time view; batch handles correctness via reprocessing.

Pros: real-time + correctness.

Cons: maintaining two pipelines for the same logic is duplication.

## Kappa architecture

Stream-only. Reprocessing is replay over the stream.

Pros: one codebase. Simpler.

Cons: requires a stream that retains history (Kafka with long retention). Reprocessing throughput must match.

## Medallion architecture

Layered transformations: bronze (raw), silver (cleaned), gold (aggregated/business-ready). Common in data lakes and Delta Lake / Databricks.

Each layer has well-defined quality and schema. Downstream consumers read from gold; data engineers work bronze and silver.

## Choosing

Ask:

1. What freshness does the destination require?
2. How frequently does data arrive?
3. How frequently is reprocessing needed?
4. What is the operational maturity for streaming?

Hours-or-days freshness, low-frequency reprocessing: batch.

Seconds freshness, high-frequency events: streaming.

Both: lambda (with the duplication cost) or kappa (with the streaming complexity).

## Anti-patterns

- **Streaming because it is modern.** Operational cost not justified.
- **Batch because we always have.** Latency requirements changed; batch no longer fits.
- **Lambda everywhere.** Duplication compounds; pick one paradigm where possible.
- **Kappa without sufficient stream retention.** Reprocessing impossible.
