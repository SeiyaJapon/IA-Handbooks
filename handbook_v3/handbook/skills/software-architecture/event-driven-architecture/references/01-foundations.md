# 01. Foundations

## Origin

Event-driven architecture emerged in enterprise systems in the 1990s and was popularised in modern usage with cloud-native systems and streaming platforms (Kafka 2011, AWS Kinesis 2013, EventBridge 2019). The underlying ideas (publish-subscribe, message-oriented middleware) predate the term.

EDA is a paradigm: it shapes how the system thinks about communication. In modern systems, EDA is treated as an architecture because it constrains structure (producers, consumers, brokers, schemas, idempotency).

## What EDA solves

- **Loose coupling** between components. Producers and consumers do not know about each other directly.
- **Multiple consumers** of the same event. New consumers can be added without changing producers.
- **Asynchronous workflows.** Long-running processes coordinated through events instead of synchronous calls.
- **Failure isolation.** A consumer failure does not bring down the producer.

## What EDA does not solve

- **Bad architecture.** EDA distributes problems instead of fixing them.
- **Latency-sensitive synchronous needs.** A query with a required response is naturally synchronous.
- **Lack of operational maturity.** EDA needs broker operations (monitoring, replay, DLQs).
- **Coordination of strict transactions.** EDA is eventually consistent.

## When EDA applies

- Workflows that are naturally asynchronous.
- Multiple consumers of the same event.
- Decoupled component evolution.
- Scale that justifies a broker.

## When EDA does not apply

- Small system with one team and synchronous interactions.
- No broker operations maturity.
- Strict consistency requirements that are hard with eventual consistency.

## Common misreadings

- **"EDA = Kafka."** Kafka is one broker. EDA is the paradigm.
- **"Every state change is an event."** Domain events are business facts, not CRUD shadows.
- **"EDA = microservices."** Microservices is deployment; EDA is communication. They compose.
- **"Async means simple."** EDA is harder to reason about and debug than synchronous code. Without observability, it is guessing.

## Decision

Ask:

1. Are workflows naturally asynchronous?
2. Are multiple consumers of the same event expected?
3. Is decoupled component lifecycle valuable?
4. Is operational maturity for the broker in place?

Two no's: EDA is not the right paradigm. Sync calls or simpler coordination suffice.
