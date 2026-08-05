# 08. User Preferences (Francisco)

Project conventions for EDA in Francisco Pérez's projects at Kintai.

## When EDA is acceptable

EDA is the **preferred default** for inter-context and inter-service communication when:

- The interaction is naturally asynchronous.
- Multiple consumers of the same event are likely.
- Decoupled lifecycle is valuable.
- Operational maturity exists.

For cross-context communication that is naturally synchronous (a query needing immediate response), a published interface (SDK or HTTP) is acceptable. Mixing sync and async in the same context map is normal; each pair is a deliberate choice.

## Event design

- Events are business facts in past tense (`OrderPlaced`, `PaymentApproved`).
- No CRUD events (`OrderUpdated`).
- No technical events (`EntitySaved`).
- One owner per event.
- Required fields: `eventName`, `aggregateId`, `aggregateType`, `occurredOn`, `eventId`, plus payload via `toPayload()`.

(See also DDD skill for domain event conventions.)

## Brokers

Default broker for the Kintai backend: **AWS EventBridge** for cross-context integration events. **AWS SQS** for queue-based async work (consumers).

The term `consumer` is the canonical name for SQS-based workers (and equivalents). Other terms (`worker`, `listener`, `subscriber`) are not used.

## Delivery semantics

- At-least-once is the default.
- Consumers are idempotent.
- Dedup tables (inbox pattern) are used where the operation is not naturally idempotent.

## Outbox

Producers that must not lose events use a **transactional outbox**:

- Event written to outbox table inside the same DB transaction as the state change.
- A relay reads the outbox and publishes to the broker.
- Outbox rows marked published or deleted on success.

This is the pattern already implemented in `shared-context/` of the Kintai backend.

## Dead-letter queues

Every consumer has a DLQ configured. DLQ growth is alerted.

## Workflows

- Multi-step async workflows use sagas (choreographed) for simple flows or AWS Step Functions (orchestrated) for complex flows.
- Sagas have explicit compensations and are observable.

## Observability

- Distributed tracing across producers and consumers (X-Ray or OpenTelemetry).
- Correlation IDs flow through events.
- Dashboards: event rates, queue depths, DLQ growth, saga states.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the project standardises on EventBridge for all cross-context events, or whether SQS is also used directly for some integrations.
- Whether the schema registry is centralised (one registry for all events) or per-context.
- Whether consumers always live in the consuming context's `infrastructure/`, or whether some are deployed as standalone Lambdas.

These are open. Do not assume; ask.
