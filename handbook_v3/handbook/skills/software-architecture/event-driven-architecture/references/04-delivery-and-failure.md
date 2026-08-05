# 04. Delivery and Failure

## Delivery semantics

Three delivery semantics, three trade-offs.

### At-most-once

Messages may be lost. Never duplicated.

Use when: loss is acceptable (some metrics, some logs). Almost never the right choice for business events.

### At-least-once

Messages are not lost. May be delivered multiple times.

Use when: loss is unacceptable. Consumers must be **idempotent**. The default for most modern brokers.

### Exactly-once

Each message is delivered once, no loss, no duplicates.

Reality: hard to achieve, expensive, often constrained to specific scenarios (Kafka with idempotent producers and transactions). Most systems achieve "effectively exactly once" by using at-least-once + idempotent consumers.

## Idempotency

A consumer is idempotent if processing the same event twice produces the same result as processing it once.

Patterns:

- **Inbox / dedup table.** Consumer records processed event IDs; duplicates are no-ops.
- **State-based.** The consumer reads current state, applies the event, the operation is naturally idempotent (set to value, not increment).
- **Conditional update.** "Update only if version = X". Fails on duplicate.

A non-idempotent consumer in an at-least-once system causes effects to multiply on retry.

## Transactional outbox

The producer's challenge: emit an event after a state change is committed, atomically.

Naive approach: commit state change, then publish event. Fails if the broker is down between commit and publish; the event is lost.

Outbox pattern:

1. Producer commits the state change AND writes the event to an `outbox` table in the same transaction.
2. A relay process reads the outbox and publishes to the broker.
3. Once published, the relay marks the row published (or deletes it).

This guarantees the event is not lost; the broker may be briefly unreachable but the event is in the outbox.

## Inbox / dedup table

The consumer's challenge: at-least-once delivery means duplicates are possible.

Inbox pattern:

1. Consumer receives event with ID `e123`.
2. Consumer checks an `inbox` table for `e123`. If present, no-op.
3. If not present, consumer processes the event and writes `e123` to the inbox in the same transaction as the state change.

This guarantees the event is processed at most once on the consumer side (effectively exactly-once).

## Dead-letter queues (DLQ)

Messages that fail repeatedly should not loop forever. After N retries, they go to a DLQ for manual handling.

Configuration:

- Max retries per message.
- Backoff strategy (exponential).
- DLQ destination.
- Alerting on DLQ growth.

A system without DLQs has two failure modes:

- Bad messages block the queue (head-of-line blocking).
- Bad messages cause infinite retries, hammering the system.

## Ordering

Many brokers do not guarantee global ordering. Some guarantee per-partition (Kafka), some per-queue (some SQS configurations).

Consumers must:

- Be idempotent.
- Tolerate out-of-order events when possible.
- Use ordering keys when order is required (Kafka partitions, SQS FIFO with message group ID).

A consumer that assumes global order in a broker that does not guarantee it is a bug waiting to surface.

## Backpressure

When consumers cannot keep up with producers, messages accumulate.

Mitigations:

- **Scale consumers.** More instances.
- **Throttle producers.** Slow down or buffer.
- **Reject and retry.** Producer queues internally and retries later.
- **Apply backpressure signals.** Reactive streams patterns where supported.

A system without backpressure handling fails when load spikes.

## Anti-patterns

- **At-most-once for business events.** Loss is unacceptable; the team accepts it because "retry exists".
- **Consumers without idempotency.** Duplicates multiply effects.
- **No outbox for cross-process events.** Events lost when the broker is briefly unreachable.
- **No DLQ.** Bad messages block the queue or loop forever.
- **Assumed global ordering.** Consumer breaks when out-of-order events arrive.
- **No backpressure.** System fails on load spikes.

## Output

For each producer-consumer pair, the team can answer:

- Delivery semantics?
- Is the consumer idempotent?
- Is there an outbox where the producer needs one?
- Is there a DLQ?
- Are ordering requirements explicit and matched?
- How is backpressure handled?
