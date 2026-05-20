# Event-Driven Architecture

## Purpose

Review system designs that use events for inter-service communication — covering broker choice, topology, consumer design, failure handling, and operational correctness.

## Responsibilities

- Evaluate event broker selection and configuration
- Assess choreography vs orchestration decision
- Review event schema design and ownership
- Evaluate consumer group design and partition strategy
- Assess dead letter queue and poison message handling
- Review ordering guarantees and their effect on consumer logic
- Evaluate backpressure and consumer lag monitoring
- Detect tight coupling disguised as event-driven design

## Instructions

- Identify the broker (Kafka, SQS, EventBridge, SNS, RabbitMQ) and check if its guarantees fit the use case
- Check delivery semantics: at-least-once, at-most-once, or exactly-once — and whether consumers are idempotent accordingly
- Check choreography vs orchestration: is the flow traceable? who owns the saga?
- Check event schema: is there a schema registry or contract? who owns the event definition?
- Check consumer error handling: is there a DLQ? what triggers it? is the poison message recoverable?
- Check ordering: does the consumer assume order? does the broker guarantee it?
- Check observability: is consumer lag monitored? is there an alert for a stuck consumer?
- Check for hidden coupling: do consumers depend on implementation details of producers?

## Heuristics

Treat as stronger concerns when:

- Non-idempotent consumers with at-least-once delivery — duplicate events cause data corruption
- No DLQ — a poison message blocks the consumer indefinitely
- Choreography with no way to trace a business transaction end-to-end
- Consumer depending on the internal schema of a producer without a contract
- Events carrying commands ("do X") instead of facts ("X happened") — inverts responsibility
- No consumer lag monitoring — silent failure risk
- Ordering assumed but not guaranteed by the broker or partition key design
- Fan-out creating dozens of consumers for a single event without lifecycle management

Treat as acceptable when:

- No schema registry for internal, low-risk events between closely related services
- Simple choreography for two-service flows where the trace is trivially observable

## Rules

- Consumers must be idempotent when delivery is at-least-once
- Every consumer needs a DLQ or an explicit poison message strategy
- Events carry facts, not commands — the producer must not know what the consumer will do
- Consumer lag must be monitored in production

## Activity Traceability

🔧 Loading skill: `event-driven-architecture`
