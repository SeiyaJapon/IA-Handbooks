# Event-Driven Architecture Skill

Use this skill when designing, reviewing, or migrating to systems where **events are the primary mode of communication**. Triggers: EDA, event-driven, event bus, message broker, choreography, orchestration, async workflows, pub/sub at the architectural level.

## What EDA is

Event-Driven Architecture (EDA) is a paradigm where components communicate primarily through **events**: facts emitted by one component, consumed asynchronously by others. The system reacts to events more than it serves direct requests.

Defining properties:

- **Asynchronous communication.** Producers emit; consumers process later.
- **Loose coupling.** Producers do not know who consumes.
- **Event contracts.** Events have schemas; the schema is the contract.
- **Multiple consumers.** Several components can subscribe to the same event.

## What EDA is and is not

EDA is a **paradigm** treated as an architecture in modern systems because it deeply constrains structure.

EDA is NOT:

- The same as Event Sourcing. ES persists events; EDA communicates with events.
- The same as messaging. Messaging (queues, brokers) is the technology; EDA is how the system uses it as the primary mode of communication.
- A synonym of microservices. Microservices can be sync; EDA can run inside a monolith.
- A solution to bad architecture. EDA on top of bad architecture is hidden chaos.

## Non-negotiable rule

Never start EDA work from "let's use Kafka" or "let's add an event bus".

The mandatory order is:

1. Identify the workflows that benefit from async (multiple consumers, decoupled lifecycle, eventual consistency acceptable).
2. Identify the events: business facts in past tense, not technical signals.
3. Define the event contracts: schemas, ownership, versioning.
4. Decide choreography vs orchestration per workflow.
5. Decide delivery semantics (at-least-once, exactly-once is rare).
6. Pick the broker.
7. Build producers and consumers with idempotency, dead-letter queues, observability.
8. Only then discuss specific platforms and frameworks.

## Hard rules

### Events are business facts in past tense

`OrderPlaced`, `PaymentApproved`, `ScenarioCreated`. Not `CreateOrder` (command), not `ProcessOrder` (action), not `OrderUpdated` (CRUD shadow).

### Producer owns the schema

The producing component owns the event schema. Consumers conform. Schema changes follow a versioning strategy.

### Consumers are idempotent

At-least-once delivery means the same event may arrive multiple times. The consumer's effect must be the same regardless.

### Dead-letter queues

Messages that fail repeatedly go to a separate queue for manual handling. No silent loss; no infinite retry.

### Observability is part of the architecture

Distributed tracing across producers and consumers. Without it, debugging an event-driven system is guessing.

## Forbidden shortcuts

- "EDA = Kafka."
- "Every state change is an event."
- "We have an event bus, so we are EDA."
- "EDA = microservices."
- "Async means we do not need contracts."

## Mandatory review behavior

When reviewing an EDA system, check:

1. Are events business facts, not technical signals?
2. Are event contracts documented and versioned?
3. Are consumers idempotent?
4. Is delivery at-least-once with dedup, or at-most-once with acceptance of loss?
5. Are dead-letter queues configured?
6. Are sagas or workflows observable?
7. Is the choice between choreography and orchestration deliberate?
8. Is the broker's operational maturity sufficient (monitoring, replay, scaling)?

## When to pick EDA

- Asynchronous workflows dominate.
- Multiple consumers of the same event are the norm.
- Decoupled component lifecycle is valuable.
- Operational maturity for a broker exists.

## When NOT to pick EDA

- Most interactions are naturally synchronous (queries with response).
- Small system, no broker-operations maturity.
- Team is unfamiliar with idempotency, ordering, dead-letter handling.

## References

- `references/01-foundations.md` for what EDA is, when it applies.
- `references/02-events-and-contracts.md` for event design, schemas, ownership, versioning.
- `references/03-choreography-vs-orchestration.md` for the two coordination styles.
- `references/04-delivery-and-failure.md` for at-least-once, idempotency, dead-letter, transactional outbox.
- `references/05-relationship-with-other-disciplines.md` for EDA vs DDD, microservices, ES, hexagonal/clean/onion.
- `references/06-anti-patterns.md` for technical events, no contracts, no idempotency, no DLQ.
- `references/07-review-checklist.md` for reviewing an EDA system.
- `references/08-user-preferences.md` for Francisco's project conventions.
