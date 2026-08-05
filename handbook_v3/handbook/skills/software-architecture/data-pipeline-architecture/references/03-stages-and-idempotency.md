# 03. Stages and Idempotency

A pipeline is a sequence of stages. Each stage transforms data and passes it to the next. Idempotency is the non-negotiable property at every stage.

## Stage shapes

- **Source.** Read data from a system.
- **Transform.** Apply business or technical transformations.
- **Aggregate.** Combine many records into summaries.
- **Enrich.** Add data from another source.
- **Validate.** Check schema and quality rules.
- **Sink.** Write to the destination.

A stage is one transformation, not many. Composing stages explicitly makes the pipeline understandable.

## Idempotency

A stage is idempotent if reprocessing produces the same result as processing once. Required for:

- **Retries.** Retry a failed stage without duplicating effects.
- **Backfill.** Reprocess historical data without corrupting current state.
- **Recovery.** Resume from a checkpoint after a crash.

## Patterns for idempotency

- **Upsert (merge).** Insert or update by primary key. Reprocessing the same record produces the same row.
- **Replace partition.** Reprocess overwrites a partition (e.g. day's data) wholesale.
- **Dedup table.** Track processed event IDs; skip duplicates.
- **Pure transformations.** Output is a deterministic function of input. Same input, same output.

What is NOT idempotent:

- **Append-only writes.** Reprocessing duplicates records.
- **Counters and increments.** Reprocessing multiplies effects.
- **External side effects without dedup.** Sending an email twice.

## Stateful stages

Some stages have state: aggregations, joins, window operations. State must be:

- **Checkpointed.** Persisted so the stage can resume.
- **Recoverable.** A failure does not lose state.
- **Bounded.** State does not grow without limit.

Streaming systems (Flink, Kafka Streams, Spark Structured Streaming) provide checkpointing primitives.

## Schema evolution

Each stage's input and output have schemas. As schemas evolve:

- Backward-compatible changes (new optional fields) do not break downstream stages.
- Breaking changes require migration: dual-running, schema versioning, downstream updates.

A pipeline without schema management breaks downstream when the source changes.

## Stage independence

Stages communicate through their outputs (a table, a stream, a topic). They do not call each other directly. This allows:

- Each stage to be tested in isolation.
- Stages to scale independently.
- Stages to be replaced or upgraded individually.

A pipeline where stage A reaches into stage B's internal state is not a pipeline; it is a tangled program.

## Anti-patterns

- **Non-idempotent stage in a retry-enabled pipeline.** Retries duplicate effects.
- **Stage that writes to the next stage's destination directly.** Skips the contract.
- **State that grows without bound.** Memory exhaustion.
- **Schema change without migration.** Downstream breaks silently.
- **Stages that are not testable in isolation.** Hard to reason about, hard to debug.

## Output

For each stage, the team can answer:

- What does it transform?
- Is it idempotent? How?
- Does it have state? Is the state checkpointed?
- What is its input and output schema?
- Is it tested in isolation?
