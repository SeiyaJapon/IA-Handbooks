# Database Design Skill

## When to use

The user asked about persistence models, queries, transactions, migrations, indexes, constraints, or data ownership.

## When not to use

- ETL/streaming pipelines: `data-pipeline-architecture`.
- AI / RAG corpus storage: `rag-design`.
- Domain modelling (aggregates, value objects): `ddd`.
- Architectural placement of persistence (port/adapter, repository): the relevant architecture skill (`hexagonal-architecture`, `clean-architecture`, `onion-architecture`).
- Data ownership across services: `microservices-architecture` (each service owns its data).

## Inputs to inspect first

- Database engine and version, hosting model (managed, self-hosted).
- Schema: tables, types, constraints, foreign keys, indexes.
- Migrations: how they are run, rollback policy, gap-free versioning.
- Query patterns: read-heavy, write-heavy, OLTP vs analytical.
- Transaction boundaries: where transactions start and end.
- Concurrency model: optimistic, pessimistic, isolation level.
- Data ownership: who writes, who reads, sharing across services or contexts.

## How to work

1. Inspect schema and queries together; queries reveal missing indexes and over-normalised paths.
2. Walk concerns: index design, transaction scope, isolation, migrations, constraints, partitioning, replication.
3. Group findings by severity. Do not modify schema unless asked.

## Output

Findings cited by table/column, grouped: blockers (missing constraints on regulated data, dangerous migrations, unindexed hot paths), defects (over-normalised joins, weak transaction boundaries, brittle migrations), nits (naming, ordering).

## Escalation

- Architectural placement of persistence: relevant architecture skill.
- Cross-service data sharing: `microservices-architecture`.
- ETL/streaming over the data: `data-pipeline-architecture`.
- Audit, retention, regulated data: `compliance-patterns`.
- Performance analysis: `performance-analysis`.
- Security of data at rest: `security-review`.

---

## Purpose

Review database schema changes, migrations, query patterns, and indexing strategies for correctness, performance, and migration safety.

## Responsibilities

- Evaluate schema changes for correctness and completeness
- Review migration safety: locking risk, data loss risk, reversibility
- Assess index strategy relative to actual query patterns
- Detect N+1 query patterns or missing eager loading
- Review nullability, default values, and constraint decisions
- Evaluate foreign key integrity and cascade behavior
- Assess separation of DDL migrations from data migrations
- Review rollback path for every migration

## Instructions

- Start from migration files and model changes
- For each new column: check nullability, default, and constraint alignment with application code
- For each migration on a large table: assess locking risk and online DDL strategy
- For each new query: check index coverage with `EXPLAIN` or equivalent
- For each new relationship: check cascade behavior and referential integrity
- Check that data migrations are separate from schema migrations
- Check that every migration has a safe rollback path or is explicitly marked irreversible

## Heuristics

Treat as stronger concerns when:

- `NOT NULL` without default on a table with existing rows
- `ALTER TABLE` on a large table without an online DDL strategy
- Missing index on a foreign key column
- N+1 patterns in the service or repository layer
- DDL and DML mixed in the same migration
- Irreversible migrations without explicit documentation
- Cascading deletes on sensitive or large tables
- Missing transaction wrapping on multi-step data operations

Treat as acceptable when:

- Missing index on a small table with infrequent queries
- Nullable column when the field is genuinely optional
- Simple migrations without rollback script when reversal is trivially obvious

## Rules

- Always check nullability and default value together
- Flag any migration that cannot be safely rolled back
- Never mix DDL and data migrations in the same file
- Cascading deletes require explicit review

## Activity Traceability

🔧 Loading skill: `database-design`
