# 05. Failure Modes

In a monolith, failure is local. In microservices, failure is distributed. Each service can fail independently, and the system must handle every failure mode.

This document lists the common failure modes and the mitigations.

## Cascading failure

Service A calls B synchronously. B is slow. A blocks waiting. Callers of A also block. The system fails everywhere because B is slow.

Mitigations:

- **Timeouts.** Every synchronous call has a timeout. The timeout reflects the caller's tolerance, not the callee's typical latency.
- **Circuit breakers.** When B fails repeatedly, A stops calling B for a period and fails fast or returns a fallback.
- **Bulkheads.** Resource pools per dependency, so one slow dependency does not exhaust all threads or connections.

## Partial failure

Some operations succeed, some fail. The system is in an inconsistent state.

Mitigations:

- **Idempotency.** Retries are safe.
- **Sagas with compensation.** Long-running workflows with explicit compensation steps when something fails.
- **Eventual consistency.** Accept that the system is briefly inconsistent and converges.

## Duplicate processing

At-least-once delivery means consumers may receive the same event twice.

Mitigations:

- **Idempotent consumers.** The same event processed twice produces the same result.
- **Inbox / dedup tables.** Consumer records processed event IDs; duplicates are no-ops.

## Lost events

Events emitted by a service do not reach the broker (broker down, network failure between commit and publish).

Mitigations:

- **Transactional outbox.** Producer writes the event to a database table inside the same transaction as the state change. A relay reads the outbox and publishes to the broker. The broker may be down for a while; events are not lost.

## Out-of-order events

Many brokers do not guarantee global ordering. Events arrive at the consumer in a different order than they were emitted.

Mitigations:

- **Per-key ordering.** Brokers like Kafka guarantee order within a partition; choose partition keys carefully.
- **Idempotent and commutative handlers.** Order does not matter for the outcome.
- **Versioning / sequence numbers.** Consumers detect out-of-order events and either reject or buffer.

## Service unavailable

A service is down. Its callers must handle this case.

Mitigations:

- **Fallback responses.** Default values, cached responses, degraded UI.
- **Async alternatives.** If the operation can be deferred, queue it and retry.
- **Graceful degradation.** Some features unavailable, others continue.

## Slow consumer

A consumer cannot keep up with the event rate. Events accumulate.

Mitigations:

- **Backpressure.** Producers slow down or buffer.
- **Scaling.** More consumer instances.
- **Dead-letter queue.** Messages that fail repeatedly go to a separate queue for manual handling.

## Operational failures

- **Deployment failure.** A bad deploy of one service breaks others. Mitigation: blue-green, canary, automated rollback.
- **Configuration drift.** A service's config differs across environments. Mitigation: config as code, audited.
- **Observability gap.** A failure happens but nobody sees it. Mitigation: logs, metrics, distributed tracing, alerts.

## Anti-patterns

- **No timeouts on synchronous calls.** A blocked thread holds resources forever.
- **Retries without backoff.** A failed dependency is hit harder by retrying clients, making the failure worse.
- **No circuit breaker.** Cascading failure is the default.
- **No idempotency.** At-least-once delivery duplicates effects.
- **No dead-letter queue.** Bad events block the queue forever.
- **No outbox.** Events are lost when the broker is briefly down.
- **No tracing.** A failed request cannot be debugged across services.

## Output

For each service, the team can answer:

- What happens if a downstream is slow, down, or wrong?
- Are timeouts, circuit breakers, retries with backoff in place?
- Are consumers idempotent?
- Is there an outbox where one is needed?
- Is there a dead-letter queue?
- Is the system observable end-to-end?

Without these, microservices is a fragile distributed monolith.
