# 07. Review Checklist

## Step 1: Does EDA apply?

1. Are workflows naturally asynchronous?
2. Are multiple consumers of the same event expected?
3. Is decoupled component lifecycle valuable?
4. Is broker operations maturity in place?

**Stop condition.** Two no's: recommend a less broker-heavy approach.

## Step 2: Event design

1. Are events business facts in past tense?
2. Are there technical events leaking into the stream?
3. Are events small (carry IDs and minimal data)?
4. Does each event have one owner?

## Step 3: Contracts

1. Are event schemas documented?
2. Is there a schema registry or equivalent?
3. Is versioning strategy defined?
4. Are breaking changes managed (migration windows, dual-publishing)?

## Step 4: Delivery

1. Delivery semantics? (At-least-once is the default.)
2. Are consumers idempotent?
3. Are dedup mechanisms in place where needed?
4. Are there assumptions about ordering that the broker does not guarantee?

## Step 5: Producer reliability

1. Is there a transactional outbox where event loss is unacceptable?
2. Is the relay running and monitored?
3. Are events emitted after state commits?

## Step 6: Consumer reliability

1. Are there dead-letter queues?
2. Are retries bounded with backoff?
3. Are bad messages alerted on?

## Step 7: Workflows

1. Are workflows choreographed or orchestrated, deliberately?
2. Are sagas (if any) observable, with persisted state?
3. Are compensations tested?

## Step 8: Observability

1. Distributed tracing across producers and consumers?
2. Structured logs with correlation IDs?
3. Dashboards for event rates, queue depths, DLQ growth?
4. Alerting on stuck sagas and DLQ growth?

## Summary

Top three findings. Quick wins. Backlog. Confirmed strengths.

## Forbidden conclusions

- "It is fine because we use Kafka."
- "It is fine because async is loosely coupled."
- "It is fine because messages are flowing."

The properties are event design, contracts, delivery semantics, producer/consumer reliability, workflow visibility, observability. Broker name and message flow alone do not validate EDA.
