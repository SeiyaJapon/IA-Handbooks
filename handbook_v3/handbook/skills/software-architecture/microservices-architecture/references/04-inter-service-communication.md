# 04. Inter-Service Communication

Services communicate over the network. The choice of communication style shapes the system's failure modes, latency, and coupling.

## Two main styles

### Synchronous (request/response)

A calls B, waits for the response, continues. HTTP, gRPC, GraphQL.

Use when:

- The caller needs the response immediately to continue.
- The interaction is low frequency.
- The caller can tolerate B's latency and partial failure.

Risks:

- Latency stacking. Three synchronous calls in a row stack their latencies.
- Cascading failure. If B is slow or down, A is also slow or down.
- Tight coupling. A and B must be available simultaneously.

Mitigations: timeouts, circuit breakers, retries with backoff, fallbacks.

### Asynchronous (events, messages)

A publishes an event. B subscribes and handles it later. SQS, SNS, Kafka, RabbitMQ, EventBridge.

Use when:

- The interaction is naturally asynchronous (notify, log, propagate state changes).
- B does not need to respond to A.
- Decoupling is valuable: A continues even if B is down.

Risks:

- At-least-once delivery: consumers must be idempotent.
- Ordering: many brokers do not guarantee global order.
- Debugging: tracing a chain of events across services is harder than a synchronous call.

Mitigations: idempotency, dead-letter queues, observability tooling.

## Choreography vs orchestration

When a workflow spans multiple services:

### Choreography

Each service reacts to events independently. No central coordinator. The workflow emerges from the chain of events.

Pro: no single point of failure, services evolve independently.

Con: hard to debug; the workflow is not visible in any one place.

### Orchestration

A coordinator (saga, process manager) drives the sequence: calls A, waits for A's response, calls B, etc.

Pro: the workflow is visible in the coordinator; easier to debug.

Con: the coordinator becomes a hub with knowledge of multiple services; harder to evolve independently.

The choice is per workflow. Both are valid.

## Contracts

Inter-service communication is governed by contracts:

- **Synchronous contracts:** OpenAPI, gRPC `.proto`, GraphQL schemas.
- **Asynchronous contracts:** event schemas (JSON Schema, Avro, Protobuf).

Properties of a good contract:

- Documented and versioned.
- Backward-compatible changes are non-breaking; breaking changes go through a versioning strategy.
- Owned by the producer (typically); consumers conform.
- Reviewed when changing.

A contract is the **only** way services interact. No undocumented direct calls. No "internal" endpoints used by another service. Each interaction is on the contract.

## Versioning

When the contract changes:

- **Backward-compatible**: add fields, broaden enums, deprecate without removing. Producer can deploy without consumer changes.
- **Breaking**: remove fields, narrow types, change semantics. Producer and consumers must coordinate. Typical approaches: dual-publishing (producer emits both versions during transition), version in path/header, dedicated migration windows.

A contract that breaks without a migration plan kills consumers in production.

## API gateway and service mesh

Two operational components that often appear:

### API gateway

A single entry point for external clients. Handles authentication, rate limiting, request routing, sometimes aggregation.

Internal calls between services do not go through the gateway (typically).

### Service mesh

A network layer (Istio, Linkerd, AWS App Mesh) that handles service-to-service communication: mTLS, retries, circuit breakers, observability. Implemented as sidecar proxies.

Operational. Not architectural per se. Helps run microservices reliably.

## Anti-patterns

- **Synchronous chain of three or more services.** A → B → C → D synchronously. Latency adds; failure cascades.
- **At-most-once messaging.** Loses events. Almost always wrong.
- **Consumers without idempotency.** At-least-once delivery duplicates effects.
- **No contract.** Services exchange undocumented JSON. Contract changes break consumers silently.
- **Event without ownership.** Multiple services publish overlapping events; consumers cannot tell which is canonical.
- **Saga without observability.** A long-running workflow with no way to inspect a stuck instance.
- **Distributed transactions with 2PC.** Brittle, slow, replaced by sagas.

## Output

For each pair of communicating services, the team can answer:

- Sync or async?
- What is the contract?
- What is the failure mode (timeouts, retries, dead-letter)?
- Is the consumer idempotent?
- If a workflow spans services, is it choreographed or orchestrated, and is it observable?

Without these, communication is improvised and the system fails unpredictably.
