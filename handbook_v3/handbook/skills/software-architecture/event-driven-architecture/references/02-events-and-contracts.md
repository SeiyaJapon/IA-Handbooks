# 02. Events and Contracts

## What an event is

An event is a **business fact in past tense** emitted by a producer when something happens.

Properties:

- **Past tense name.** `OrderPlaced`, `PaymentApproved`, `ScenarioCreated`. Not `CreateOrder`, not `OrderUpdated`.
- **Owned by the producer.** The producer defines the event's schema.
- **Immutable.** Once emitted, the event does not change.
- **Self-contained.** Carries enough data for typical consumers; consumers may enrich by querying the producer.
- **Timestamped.** When the fact occurred (`occurredAt`).
- **Identified.** Unique event ID for dedup and tracing.

## Event vs domain event vs integration event

Three related concepts (see also DDD skill):

- **Domain event:** a business fact within a single bounded context, in the producer's vocabulary.
- **Integration event:** a fact published across context boundaries, in a published language. May be derived from a domain event.
- **System event / technical event:** infrastructure signals (Lambda invoked, S3 object created). Not domain events.

In EDA, the events that flow through the broker are typically integration events (cross-context) and sometimes domain events (within one context's internal event bus).

## Schema

The event schema is the contract. Options:

- **JSON Schema.** Common for HTTP/JSON brokers (EventBridge, SNS).
- **Avro.** Common with Kafka (with a schema registry).
- **Protobuf.** Strongly typed, common in gRPC + streaming.

The schema specifies fields, types, required/optional, and version.

## Ownership

The **producer owns the event schema**. Other consumers conform.

When multiple producers emit overlapping events, the team defines one canonical event with one owner. Multiple producers without a single owner is a smell.

## Versioning

When the schema changes:

- **Backward-compatible:** add optional fields, broaden enums. Producer can deploy without consumer changes.
- **Breaking:** remove fields, narrow types, change semantics. Requires a versioning strategy:
  - Dual-publishing during a migration window.
  - Version in the event type (`OrderPlacedV2`).
  - Schema-registry-managed migration.

A schema change without a version strategy breaks consumers in production.

## Event size

Events should be small. Pass IDs and minimal payload; consumers query the producer for details if needed.

When events grow large (full aggregate snapshots), the broker becomes a database. Reconsider: is this an event, or a state replication?

## Anti-patterns

- **CRUD events.** `OrderUpdated` instead of `OrderShipped`, `OrderCancelled`. Loses business meaning.
- **Technical events as domain events.** "EntityPersisted", "RowInserted". Infrastructure signals leaking into the domain stream.
- **No schema.** Events as untyped JSON. Consumers parse defensively; breaking changes are silent.
- **Multiple producers, no owner.** Many components emit overlapping events. Consumers cannot tell which is canonical.
- **Events that are commands.** `ProcessOrderEvent` is not an event; it is a command in disguise. Events are facts; commands are intents.

## Output

For each event in the system, the team can answer:

- What business fact does this represent?
- Who is the producer (and owner of the schema)?
- What is the schema, and where is it documented?
- What is the versioning strategy?
- Who are the known consumers?
