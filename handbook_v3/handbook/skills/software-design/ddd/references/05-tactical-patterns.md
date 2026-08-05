# 05. Tactical Patterns

Tactical DDD models the **inside of a single bounded context**: the objects, the rules, the way state changes. Tactical work happens after strategic design has produced subdomains, bounded contexts, and a context map. Skipping ahead to tactical without strategic is the canonical failure mode.

## Entities

An entity is an object whose **identity** distinguishes two instances, regardless of attribute values.

- Two `Customer` instances with the same name and email are still different customers if their IDs differ.
- Identity is conferred at creation and never changes.
- Identity is typed: `CustomerId`, not raw `string`. The type carries meaning and prevents mixing identities of different concepts.

What an entity has:

- An identity (a value object).
- Attributes that may change over its lifecycle.
- Behaviour (methods) that enforces invariants when those attributes change.

What an entity is not:

- A row in a table promoted to a class.
- A bag of getters and setters.
- A struct manipulated from outside.

If the entity has only data and no behaviour, it is **anemic**, and the rules that should belong to it have escaped into services or controllers. Move them back.

## Value objects

A value object is an object whose **attributes** define equality. Two instances with the same attributes are the same.

Properties:

- **Immutable.** No mutation methods. "Changing" produces a new instance.
- **Equality by value.** `money1.equals(money2)` is true if and only if their amount and currency match.
- **Validated at construction.** A value object that cannot be invalid does not exist as the type. An `Email` validates format on construction. A malformed email cannot be an `Email`.
- **Self-contained behaviour.** `Money.add(Money)`, `Email.domain()`, `DateRange.contains(Date)`.
- **Replaceable for primitives.** When the attribute carries domain meaning, the value object replaces the primitive.

Primitive obsession is the most common failure: amounts as `number`, currencies as `string`, dates as `Date`, IDs as `string`. Every one of those primitives is a missed value object.

Use value objects liberally. They are cheap, immutable, and they are where the language of the domain becomes type-safe.

## Aggregates

An aggregate is a cluster of entities and value objects treated as a **single consistency boundary**.

- One entity in the cluster is the **aggregate root**. It is the only entry point from outside.
- Internal entities and value objects are accessed through the root.
- The aggregate enforces invariants that span its members.

The three jobs of an aggregate:

1. **Enforce invariants.** Rules that span several attributes or members live here. The aggregate is the only place where those invariants can be checked atomically.
2. **Define the transactional boundary.** A change to an aggregate is committed as one unit. Two aggregates are two transactions.
3. **Define what loads together.** When a use case needs the aggregate, it loads the aggregate. Internal entities are not loaded separately.

Rules:

- **Reference other aggregates by ID, not by object.** An `Order` holds a `CustomerId`, not a `Customer`. If the use case needs both, it loads each through its own repository.
- **Keep aggregates small.** Large aggregates contend on writes and create transactional bottlenecks. The right size is "the smallest cluster that protects the invariant".
- **One repository per aggregate root.** No repositories for internal entities.
- **Cross-aggregate consistency is eventual.** Two aggregates that need to react to each other do so by domain event, not by reaching across the boundary.

How to size an aggregate:

- Start with what the invariant requires. If the rule is "an order's total equals the sum of its line items", line items belong inside the order aggregate.
- Reject what the invariant does not require. Customer profile data does not belong inside the order aggregate; it lives in its own aggregate.
- When in doubt, smaller is better. A too-small aggregate causes some eventual consistency. A too-large aggregate causes contention and slow operations.

## Domain services

When behaviour does not naturally belong to a single entity or value object, it belongs in a **domain service**.

Typical reasons:

- The behaviour coordinates two or more aggregates.
- The behaviour is naturally stateless (a calculation, a policy).
- Forcing the behaviour into an entity would violate the entity's responsibility.

A domain service:

- Lives in the domain layer.
- Speaks the ubiquitous language.
- Has no infrastructure concerns. No HTTP, no database, no framework, no decorators.
- Is named after what it does in domain terms: `RiskScoreEvaluator`, `EligibilityChecker`, `PriceCalculator`. Not `OrderHelper`, `ProductManager`.

A domain service is **not** a place to dump behaviour that did not fit elsewhere. If you reach for a domain service often, the model is anemic and behaviour is escaping outwards. Push it back into entities and value objects.

## Domain events

A domain event is a **business fact in the past**: something that happened, expressed in the ubiquitous language, that other parts of the system may want to react to.

Properties:

- **Past tense name.** `ScenarioCreated`, `PaymentMethodMapped`, `RiskScoreRecalculated`. Not `CreateScenario` (command), not `ScenarioCreator` (service).
- **Tied to an aggregate.** The event includes `aggregateId` and `aggregateType`. The aggregate that produced it owns the schema.
- **Timestamped.** `occurredOn` is the moment the fact happened from the aggregate's point of view.
- **Immutable.** Once recorded, the event does not change.
- **Carries a payload.** The data subscribers need, separate from the aggregate's full state.

How events are produced:

1. The aggregate, when its state changes, calls `record(new SomethingHappened(...))`.
2. After persistence, the application service calls `aggregate.pullDomainEvents()` and dispatches them.
3. Subscribers (in the same context or in others) react.

Two roles for events:

- **Inside a context.** Decoupling aggregates that need to react to each other within the same context. Eventually consistent: the listener processes the event in a separate transaction.
- **Across contexts.** The canonical way for one bounded context to inform others. The publishing context owns the schema; consuming contexts subscribe and translate (often through an ACL).

A domain event is not an integration event in disguise. They are related but not the same:

- **Domain event.** A fact within the source context's language. May or may not cross context boundaries.
- **Integration event.** A fact published across context boundaries, in a published language. Often derived from a domain event but reshaped for external consumption.

When a domain event is published externally, its schema becomes part of the published language. Changing it requires consumer coordination. See `07-integration-patterns.md` for cross-boundary patterns.

### Strong vs weak domain events

Not every event class is a domain event. The bar:

- **Strong domain events.** Represent a completed business action. Express something meaningful in the ubiquitous language. Enable decoupled reactions that the business itself would recognise. Align with aggregate boundaries. The aggregate produces them as a consequence of a state change with business meaning.
- **Weak or problematic events.** Describe technical steps (`EntitySaved`, `DbUpdated`, `RowInserted`). Mirror CRUD actions one-for-one (`OrderUpdated` instead of `OrderShipped` or `OrderCancelled`). Leak infrastructure or persistence details. Are used to replace direct calls without real decoupling benefit. Introduce unclear timing or ordering. Create hidden dependencies between modules or services that nobody designed deliberately.

Operational rules:

- Events must represent business facts, not technical operations.
- Avoid events that only mirror CRUD. If the only signal is "something changed", the event has no business meaning and is decoration.
- Make timing and consistency expectations explicit when introducing an event: when is it produced, when is it dispatched relative to the transaction, who is expected to react.
- Do not introduce events if they do not improve the design. The fact that domain events exist as a pattern does not mean every interaction should use them. Direct calls within an aggregate, or within a single use case, are often clearer.
- When in doubt, weigh the coupling the event removes against the coupling the event introduces (the producer commits to the schema, every consumer commits to handling it).

## Repositories

A repository is the abstraction that lets the application layer load and save aggregates without knowing how they are persisted.

Properties:

- **Per aggregate root.** No `LineItemRepository` if line items live inside `Order`. There is `OrderRepository`.
- **A contract owned by the core.** The repository is an interface defined where the domain (or application) needs it. How that interface materialises and where it lives physically is an architectural decision (see the architecture skill in use, e.g. `hexagonal-architecture`).
- **Speaks domain language.** `findById(OrderId)`, `findActiveScenariosFor(CustomerId)`. Not `query(filter)`, not `findAll()` returning everything.
- **Not generic.** A `Repository<T>` is a sign that persistence is driving the design.
- **Hides persistence.** The caller does not know if the storage is SQL, NoSQL, file, or in-memory.

What a repository does not do:

- Enforce invariants. That is the aggregate's job.
- Coordinate use cases. That is the application service's job.
- Expose query language. That defeats the abstraction.

If a use case needs a query that does not fit the aggregate-loading shape (a list of summaries, a report), that query may not belong in the repository at all. It may belong in a separate read model (see `07-integration-patterns.md` for CQRS).

## Factories

A factory encapsulates the construction of an aggregate when construction itself contains business logic.

When to use:

- Construction has multiple steps with derived values.
- Construction must enforce invariants that span the initial state.
- Construction depends on policies that the aggregate's constructor cannot easily express.

When not to use:

- The aggregate's constructor is enough. A factory then is ceremony.
- The factory is a glorified builder pattern with no domain meaning. That is a builder, not a factory.

The factory is a domain artefact. It speaks the ubiquitous language. It does not know about persistence, framework, or HTTP.

## Application services vs domain services

These two are routinely confused.

- **Domain service.** Lives in the domain layer. Coordinates domain objects. Contains business behaviour. No knowledge of transactions, framework, or persistence.
- **Application service** (use case, command handler, query handler). Lives in the application layer. Orchestrates: loads aggregates from repositories, calls domain methods, pulls events, saves, dispatches events. Thin. Knows about transactions and orchestration. Knows nothing about HTTP or framework.

If an application service starts containing decisions about whether something is allowed, those decisions are escaping the domain. Move them back into entities, value objects, or domain services.

A typical command flow, expressed in DDD terms:

1. The entry point (whatever delivery mechanism the architecture defines for this case) receives input and produces a command.
2. The application service loads the aggregate(s) it needs through the repository contract.
3. The application service calls a method on the aggregate. The aggregate enforces invariants and records domain events.
4. The application service saves the aggregate through the repository contract.
5. The application service pulls the recorded events and dispatches them through the event bus contract.
6. The entry point returns the response.

Each piece has one job. The aggregate does not save itself. The repository does not enforce invariants. The entry point does not dispatch events. The shape of "entry point" (HTTP controller, CLI handler, queue consumer, etc.) is an architectural decision, not a DDD one; see the architecture skill for that.

## Anemic domain anti-pattern

The most common failure of DDD-by-name-only is the anemic domain: classes that look like entities but expose getters and setters, with all behaviour in services that read and write the entity's fields from outside.

Symptoms:

- Entities with `get`/`set` for every attribute and no other methods.
- "Service" classes that read an entity, mutate it field by field, save it.
- Invariants enforced in controllers or services, not in the entity.
- Domain events raised by the application layer, not by the aggregate.

Cure: move behaviour back into the entity. The entity decides what it can become next. The service asks it.

## Other tactical anti-patterns

- **One repository per entity.** Repositories are per aggregate root. Internal entities do not have repositories.
- **Aggregate per table.** Aggregates are designed from invariants, not from the database schema.
- **Value object as a struct.** A value object that is just a bag of fields without behaviour or validation is misnamed. It is a DTO, not a value object.
- **Domain event as a CRUD notification.** "EntityChanged" is not a domain event; it is a database trigger reflavoured. A domain event names a business fact: `ScenarioApproved`, not `ScenarioUpdated`.
- **Application service that contains rules.** "If the order's total is over X, set discount". That rule belongs inside the order aggregate, not in the application service that calls `setDiscount()`.

## Output of tactical work

For each bounded context, when tactical work is done enough to start writing code:

- The aggregates and their roots are listed.
- The invariants enforced by each aggregate are written down.
- The value objects are identified.
- The domain services (if any) are listed.
- The domain events are listed, with their producers.
- The repositories (one per aggregate root) are listed.
- The application services (use cases) are listed, with their flow.

Without these, tactical implementation is guessing.
