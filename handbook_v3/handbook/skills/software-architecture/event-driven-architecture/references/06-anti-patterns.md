# 06. Anti-patterns

## Technical events as domain events

Events named `EntitySaved`, `RowInserted`, `LambdaInvoked`. Infrastructure signals leaking into the event stream.

Fix: events are business facts. Filter and translate infrastructure signals into meaningful events at the producer.

## CRUD events

`OrderUpdated` instead of `OrderShipped`, `OrderCancelled`, `OrderApproved`. Loses business meaning; consumers cannot tell what happened.

Fix: events express the business action, not the database operation.

## No contracts

Events are untyped JSON. Consumers parse defensively. Schema changes break consumers silently.

Fix: documented schemas (JSON Schema, Avro, Protobuf). Schema registry where applicable.

## No idempotency

Consumers process duplicates as if new. Effects multiply on retry.

Fix: idempotent handlers, dedup tables.

## At-most-once messaging

Events lost on broker failure. The team accepts loss "because retry exists".

Fix: at-least-once delivery, transactional outbox to prevent producer-side loss.

## No dead-letter queue

Bad messages block the queue or loop forever, hammering the system.

Fix: DLQ with bounded retries and alerting.

## Assumed global ordering

Consumer breaks when out-of-order events arrive.

Fix: design for the broker's ordering guarantees; use partition keys when order matters.

## Implicit cycles

Component A reacts to B's events; B reacts to A's events. Infinite loop.

Fix: identify cycles in the event graph; break them deliberately.

## Saga without compensation

A long-running workflow that fails partway leaves the system inconsistent.

Fix: every saga step has a compensation. Compensations are tested.

## Saga without observability

Stuck sagas cannot be detected or inspected.

Fix: persist saga state. Build dashboards. Alert on stuck sagas.

## Distributed monolith via events

Components are tightly coupled through chains of events. A change in one event ripples through many components.

Fix: review event design. Events should be coarse business facts, not fine-grained signals.

## Multiple producers without owner

Several components emit overlapping events. Consumers cannot tell which is canonical.

Fix: one owner per event. Coordinate producers.

## "EDA = Kafka"

The team adopted Kafka and called it EDA. Operational concerns are not handled (no DLQ, no monitoring, no schema registry).

Fix: EDA is a paradigm; Kafka is a broker. The architecture is the design of events, contracts, and failure handling.

## Event without consumer

Events emitted that nobody consumes. Cluttering the bus, costing operational overhead.

Fix: prune unused events. Events have purposes; without a consumer, they have no purpose.

## Event payload as full state

Events carry the full aggregate snapshot. The broker becomes a database.

Fix: events carry minimal data plus an ID. Consumers query for details if needed. (Exception: when the event is itself the source of truth in Event Sourcing.)

## Output

When reviewing EDA, look for these patterns by name. Technical events, no contracts, no idempotency, no DLQ are the most common.
