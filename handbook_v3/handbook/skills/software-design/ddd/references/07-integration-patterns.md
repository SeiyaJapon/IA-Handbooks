# 08. Integration Patterns

This document covers patterns that are **adjacent to DDD** but not part of the DDD discovery flow: messaging between contexts, integration events, CQRS, Event Sourcing, and Event-Driven Architecture.

These patterns are introduced **only after** the strategic model, bounded contexts, context map, aggregates, and use cases are understood. They are answers to specific problems that the domain model surfaces; they are not defaults.

## When to read this document

Read this when:

- The team has decided that contexts must communicate, and the question is "how".
- A read model has become so different from the write model that mixing them is a cost.
- Operational requirements (audit, replay, temporal queries) make the event log a candidate as source of truth.
- Inter-context coupling has become synchronous and fragile, and async messaging is being considered.

Do not read this to "modernise" a design that is not yet sound. CQRS, Event Sourcing, and EDA layered onto a confused domain model produce a confused, distributed mess.

## Domain events vs integration events

Two related but different concepts.

- **Domain event.** A fact within the source context's language. May or may not cross a context boundary. Owned by the aggregate that produced it.
- **Integration event.** A fact published across context boundaries, in a published language. Often derived from a domain event but reshaped for external consumption.

Why they are not the same:

- A domain event uses the source context's vocabulary. Other contexts might not understand it.
- An integration event uses the published language between contexts. It is stable, versioned, and decoupled from the source context's internal model.
- A change in the source context's domain event should not force every consumer to redeploy. The published integration event sits between them.

Practical consequence: when a context publishes externally, the producer translates its internal domain event into one or more integration events. The consumer subscribes to integration events, not to domain events.

When the contexts are within the same monorepo and the team owns both ends, this translation may collapse into "use the same event class". That is a shortcut. The day the contexts diverge or split processes, the absence of the translation hurts.

Before publishing any event across a boundary, apply the strong vs weak filter from `05-tactical-patterns.md`: an event that mirrors CRUD or that exists only to "notify something changed" has no business meaning and should not become an integration event. Cross-boundary events deserve a higher bar than internal events because their schema becomes part of the published language and binds every consumer.

## Messaging between contexts

Three operational decisions:

- **Synchronous vs asynchronous.** Async by default. Synchronous (HTTP, RPC) only when the consumer needs the response immediately and cannot proceed otherwise.
- **Choreography vs orchestration.** Choreography: each context reacts to events independently. Orchestration: a coordinator drives the sequence. Choreography scales better but is harder to debug; orchestration is easier to reason about but creates a coordinator with knowledge of multiple contexts.
- **At-least-once vs exactly-once.** Most brokers offer at-least-once. Exactly-once is rare and expensive. Consumers must be **idempotent**: receiving the same event twice produces the same result as receiving it once.

Patterns to know:

- **Transactional outbox.** Producer writes the event into an outbox table inside the same transaction as the aggregate change. A relay reads the outbox and publishes to the broker. Guarantees that no event is lost when the broker is down at commit time.
- **Inbox / dedup table.** Consumer records the IDs of processed events in an inbox table. Reprocessing the same event becomes a no-op.
- **Saga.** A long-running coordination across contexts, expressed as a state machine that listens to events and emits commands. Two flavours: choreography saga (no central coordinator) and orchestration saga (a process manager).
- **Anti-corruption layer.** When a context consumes events from another, it translates the foreign event into its own language at the boundary. Strongly recommended when the producer is external or unstable.

## CQRS (Command Query Responsibility Segregation)

CQRS is the separation of write operations (commands) from read operations (queries) into different models. In its mildest form, it is a code-level separation: command handlers and query handlers, possibly sharing the same database. In its strongest form, the read model is a separate store, populated asynchronously from the events of the write side.

When CQRS is justified:

- The read model needs **shapes that the write model cannot serve efficiently**: aggregations, denormalisations, full-text search, multi-context joins.
- **Read load and write load have very different scaling characteristics**.
- **Reads must be fast** and the cost of computing them on demand is too high.
- The team has the operational maturity to run two stores with replication between them.

When CQRS is not justified:

- The read model is "the same as the write model with some filters". A repository method is enough.
- The team does not have monitoring, replay, and reconciliation tooling. CQRS without those is a debugging nightmare.
- The domain is still being discovered. Splitting read and write before the model is stable doubles the cost of every change.

CQRS is **not** a default of DDD. Many DDD projects do not use it. Apply it when the read/write asymmetry justifies it, not because "DDD says so".

CQRS is also **not** a synonym of Event Sourcing. They are often combined, but each can exist without the other:

- CQRS without Event Sourcing: separate read and write models, write side persisted as state (rows in a table).
- Event Sourcing without CQRS: events as source of truth, single read/write API on top.

## Event Sourcing

Event Sourcing stores the **sequence of events** that produced an aggregate's state, instead of (or in addition to) the current state. The aggregate is rebuilt by replaying its events.

When Event Sourcing is justified:

- **The history matters.** Audit, regulatory replay, temporal queries.
- **Multiple read models** must derive from the same write side, each with its own shape.
- **The system must be able to rebuild itself** from a log (recovery, migration to new schema).
- **The team has the operational maturity** to handle event versioning, schema evolution, snapshots, replay, and dead-letter handling.

When Event Sourcing is not justified:

- The history is not used.
- The team does not have schema-evolution discipline. Once events are persisted, their schema is forever.
- The aggregates are simple and rebuilding them is wasteful compared to storing the state.

Event Sourcing implications that are easy to underestimate:

- **Event schemas are immutable in practice.** Once an event is in the log, its schema cannot change retroactively. Versioning is the rule, not the exception.
- **Replay is real.** Bringing a new read model online means replaying every event. Performance and correctness must be designed for it.
- **Snapshots are optional but necessary at scale.** Replaying a million events to load an aggregate is not viable.
- **Compensation, not deletion.** Events are not removed. A "delete" is a new event that cancels the previous one.

Event Sourcing is **not** a default of DDD. Most DDD projects do not use it. Apply it when the history is genuinely a first-class concern, not because "DDD says so".

## Event-Driven Architecture (EDA)

EDA is the architectural style where **components communicate primarily through events**. It is broader than DDD: many EDA systems do not use DDD at all.

When EDA is justified:

- **Loose coupling between components is required**. Components must evolve independently.
- **Asynchronous workflows dominate**. The system reacts to events more than it serves direct requests.
- **Multiple consumers** of the same event are the norm, not the exception.
- **The team has the broker-operations maturity**: monitoring, dead-letter queues, replay, ordering guarantees.

When EDA is not justified:

- The interactions are naturally synchronous (a query that needs a response).
- The team is small and the operational cost of brokers is disproportionate.
- The system is one application with one database and few external interactions.

EDA implications:

- **Debugging is harder.** Tracing a chain of events across consumers is more complex than tracing a synchronous call.
- **Ordering is not free.** Some brokers do not guarantee order; some guarantee order per partition. Consumers must be designed for the ordering they actually receive.
- **Failure handling shifts.** Failures are not surfaced to the caller; they go to dead-letter queues, retries, alerts.

EDA is **not** a default of DDD. DDD projects can communicate synchronously when synchronous communication is the right choice.

## How CQRS, Event Sourcing, and EDA relate to DDD

- **DDD is independent of all three.** A DDD project can use none, one, two, or all three.
- **Domain events are not Event Sourcing.** Producing domain events is a tactical pattern. Persisting them as the source of truth is Event Sourcing.
- **Cross-context communication is not EDA.** Two DDD contexts can communicate synchronously through a published interface and not be EDA.
- **CQRS does not require DDD.** A non-DDD CRUD application can split read and write models if its read/write asymmetry justifies it.

The order to consider them:

1. Apply DDD: subdomains, bounded contexts, context map, aggregates, use cases.
2. Decide cross-context communication: events vs published interface, sync vs async.
3. If async, decide messaging patterns: outbox, inbox, sagas.
4. Decide read-model separation: do reads and writes need separate models? If yes, CQRS.
5. Decide source of truth: is the event log the source of truth, or is current state? If event log, Event Sourcing.
6. Decide architectural style: are events the dominant communication mechanism across the whole system? If yes, EDA.

Each step is a separate decision. Skipping ahead is a common failure.

## Anti-patterns in integration

- **CQRS by reflex.** "We use DDD, so we use CQRS." Apply it when read/write asymmetry justifies it.
- **Event Sourcing by reflex.** "We have domain events, so we are Event-Sourcing." Producing events is not the same as persisting them as source of truth.
- **EDA by reflex.** "We need scale, so we use events for everything." EDA has operational costs that some teams cannot absorb.
- **Domain event reused as integration event.** Internal vocabulary leaks across context boundaries. The day the source context refactors, every consumer breaks.
- **Synchronous chain across three contexts.** A cascading failure waiting to happen. Break the chain with async events or accept the coupling explicitly.
- **At-most-once messaging.** Loses events. Almost always wrong; consumers should be idempotent and the system should be at-least-once.
- **Saga with no observability.** A long-running workflow that nobody can inspect when it stalls.
- **Outbox without a relay.** Events accumulate in the outbox table and are never published. The whole point of the pattern is the relay.

## Output

When integration patterns are decided enough to act on:

- For each pair of communicating contexts, the messaging style is documented (sync/async, choreography/orchestration).
- For async pairs, the broker, the topic structure, and the delivery guarantees are documented.
- The read-model separation, if any, is documented per context.
- The source of truth (state vs event log) is documented per aggregate.
- The reasons for any of CQRS, Event Sourcing, or EDA are written down. "Because DDD" is not a reason.

Without those, the integration is improvised, and the operational cost surfaces in production.
