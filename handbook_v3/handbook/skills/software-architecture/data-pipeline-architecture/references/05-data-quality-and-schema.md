# 05. Data Quality and Schema

A pipeline that processes garbage produces garbage. Quality and schema controls are part of the architecture.

## Schema management

Each stage has an input and output schema. The schemas:

- **Are documented.** Field names, types, optionality, constraints.
- **Are versioned.** Schema changes are tracked.
- **Are validated.** Records that violate the schema are caught (rejected, sent to DLQ, or coerced with a documented rule).
- **Have ownership.** One team owns each schema.

Tools: JSON Schema, Avro with schema registry, Protobuf, OpenAPI for HTTP-fed pipelines.

## Schema evolution

When a schema changes:

- **Backward-compatible.** Add optional fields, broaden enums. Producers can update without breaking consumers.
- **Forward-compatible.** Consumers can read records with future fields they ignore.
- **Breaking.** Remove fields, narrow types, change semantics. Requires migration:
  - Dual-publishing (producer emits both versions).
  - Versioned topics (`events.v2`).
  - Documented migration window.

A schema change without a strategy breaks downstream pipelines.

## Data quality checks

Validation happens at multiple points:

- **At ingest.** Reject records that violate the source schema.
- **Per stage.** Validate intermediate state.
- **At destination.** Final checks before writing to gold layer or warehouse.

Common checks:

- **Schema conformance.** Fields, types, required/optional.
- **Value constraints.** Ranges, enums, regex.
- **Referential integrity.** Foreign keys present.
- **Statistical checks.** Row count not zero; values within expected ranges.
- **Duplicate detection.** No duplicates by primary key.
- **Freshness.** Data is recent enough.

Tools: Great Expectations, dbt tests, custom validators.

## Quality dashboards

A dashboard that shows quality metrics over time:

- Record counts per stage.
- Error rates per stage.
- DLQ growth.
- Schema drift.
- Data freshness.

Without a dashboard, quality issues surface only when downstream consumers complain.

## Medallion (bronze/silver/gold) and quality

In medallion architecture:

- **Bronze:** raw data. Minimal validation. Schema may be lenient.
- **Silver:** cleaned data. Schema enforced. Bad records to DLQ.
- **Gold:** business-ready data. Strict quality. Aggregations.

Quality progressively improves through the layers. Consumers read gold; the lower layers are operational.

## Anti-patterns

- **No schema.** Records are untyped JSON. Downstream parses defensively. Breaking changes are silent.
- **No validation.** Bad data flows through, corrupting destinations.
- **No DLQ for quality failures.** Bad data is silently dropped or breaks the pipeline.
- **No quality dashboard.** Consumers detect quality issues, not the pipeline owner.
- **Schema versioning by accident.** Producers change schemas without coordinating; consumers break.

## Output

For each schema, the team can answer:

- Where is it defined? (registry, repo, doc)
- Who owns it?
- What is the versioning strategy?
- What validation runs at each stage?
- Where do invalid records go?
- What quality dashboard exists?
