# 05. Driven Adapters

A **driven adapter** is the implementation of a driven port using a specific technology. The application core defines what it needs (the port); the driven adapter satisfies that need by talking to a database, an HTTP API, a queue, a file system, a clock, or any other external resource.

Driven adapters live in `infrastructure/`. They depend on the port (in `application/` or `domain/`) and on the technology they adapt. They never leak the technology back into the core.

## Common kinds of driven adapters

### Repositories

Implement repository ports defined in the core. Persist and retrieve aggregates without exposing how.

Responsibilities:

- Translate between the aggregate's shape (in domain language) and the database's shape (rows, documents, columns).
- Execute the persistence operation (insert, update, delete, query).
- Hide the database technology (SQL, NoSQL, file).
- Hide the ORM (if used).

Non-responsibilities:

- Enforcing invariants (that is the aggregate's job).
- Coordinating use cases (that is the application service's job).
- Exposing query language to the caller (that defeats the abstraction).

Typical placement: `infrastructure/persistence/<Aggregate>.repository.ts` (or directly at the layer's root if there is only one).

### External service clients

Implement external service ports defined in the core. Call third-party APIs, internal services from other contexts, or any external system.

Responsibilities:

- Translate between the core's vocabulary (domain types) and the external API's vocabulary (DTOs, JSON, gRPC messages).
- Make the network call.
- Handle authentication, retries, timeouts, circuit breaking.
- Map the external API's errors to the core's error types.

Non-responsibilities:

- Deciding whether the call should happen (the use case decides).
- Containing business rules.
- Caching beyond a clearly delimited scope (cache decisions belong to the application or are themselves expressed as a separate driven port).

Typical placement: `infrastructure/clients/<Service>.client.ts`.

### Event bus / message publishers

Implement event bus or message publishing ports. Send domain events or integration events to a broker.

Responsibilities:

- Serialise the event into the broker's wire format.
- Publish to the configured topic / queue / exchange.
- Handle the broker's failure modes (retry, dead-letter, transactional outbox).
- Acknowledge the publish.

Non-responsibilities:

- Deciding what to publish (the application decides).
- Routing decisions beyond what the port specifies (subscriptions are an outside concern).

Typical placement: `infrastructure/messaging/<EventBus>.eventbus.ts`.

### File storage

Implement file storage ports. Read and write files (local FS, S3, GCS, blob storage).

Responsibilities:

- Translate between the core's notion of "a file" (typed payload, content, metadata) and the storage's API.
- Handle the storage's failure modes.

Typical placement: `infrastructure/storage/<Storage>.storage.ts`.

### Time and identity providers

Implement time and identity ports.

- A clock port returns the current time. The adapter is `SystemClock` in production, a controllable `FakeClock` in tests.
- An identity generator port returns a new identity. The adapter is `UuidGenerator` in production, a deterministic generator in tests.

Why these are ports: making them explicit is what allows the core to be testable without controlling time or randomness from outside.

Typical placement: `infrastructure/time/SystemClock.ts`, `infrastructure/identity/UuidGenerator.ts`.

### Notifications

Implement notification ports (email, SMS, push, webhook).

Responsibilities:

- Format the message according to the notification channel.
- Deliver through the chosen provider.
- Translate provider failures to the core's error vocabulary.

Typical placement: `infrastructure/notifications/<Channel>.notifier.ts`.

## Properties of a good driven adapter

- **Implements one port.** An adapter is a one-to-one mapping from a port to a technology.
- **Hides the technology.** The caller does not know if the storage is SQL, NoSQL, or in-memory.
- **Translates vocabulary.** The port speaks domain; the adapter speaks domain to its callers and SDK to itself.
- **Owns no business rules.** It executes; it does not decide.
- **Honours failure modes.** Knows what to retry, what to fail fast, what to surface as a domain-meaningful error.

## Translating between the core and the technology

The driven adapter is where impedance mismatch is absorbed.

- The aggregate has methods, invariants, and value objects. The database has columns. The adapter maps one to the other.
- The external API uses snake_case JSON with auto-generated IDs. The core uses domain types with typed identifiers. The adapter translates both directions.
- The queue accepts byte payloads. The core has typed events. The adapter serialises and deserialises.

This translation is not optional. An adapter that exposes the database's row directly to the application service has not done its job; the database has leaked.

## Adapters and transactions

Repositories and other database-touching adapters participate in transactions, but **they do not own them**. The transaction is opened and closed by the application service (the use case).

Two common patterns:

- **Unit of work** held by the application service, passed to the repositories.
- **Transactional outbox** when domain events must be published atomically with state changes.

In both cases, the adapter accepts the transaction context as a constructor parameter or a method parameter; it does not begin transactions on its own.

## Adapter composition

Sometimes an operation needs more than one adapter:

- A use case publishes an event through an `EventBus` port and persists state through a `Repository` port.
- A use case retrieves data from one external service and writes to another.

The composition is the **application service's** responsibility, not the adapter's. An adapter that calls another adapter has stepped outside its role.

When composition is genuinely needed at the adapter level (a transactional outbox composes a repository write and a publish in one operation), the composition is a deliberate pattern documented at the adapter, not an ad-hoc call.

## Adapters and external errors

External systems fail. The adapter translates those failures.

- A network timeout calling an external service maps to an `InfrastructureError` (retryable) or to a domain-meaningful `ServiceUnavailableError` if the contract demands it.
- A unique constraint violation in the database maps to a domain or application error (`DuplicatedScenarioError`, `ConflictError`).
- A 404 from an external API maps to a domain `NotFoundError` or to a meaningful absence (returning `null` or `Option.none()`).

The translation is part of the adapter's contract. Without it, the core has to know about HTTP statuses, SQL error codes, or queue ack failures.

## Anti-patterns

- **Driven adapter in `application/` or `domain/`.** The adapter must live in `infrastructure/`. Anywhere else inverts the dependency rule.
- **Adapter that exposes the SDK.** Methods named `executeQuery(sql)` or `httpGet(url)` mean the SDK has leaked. Redesign the port in domain terms.
- **Adapter that contains business rules.** "If the row's status is X, decide Y." The adapter only translates; the decision is the core's.
- **Adapter that owns the transaction.** "I'll begin a transaction here." The use case decides; the adapter participates.
- **Adapter that depends on another adapter directly.** Adapters are independent. Composition is the application's job.
- **Generic adapter (`Repository<T>`, `Client<T>`).** Adapters are specific. Generic shapes are a sign that the persistence model is driving the design.
- **Adapter that throws SDK exceptions.** SDK exception types should not reach the application service. The adapter translates to the core's error vocabulary.

## Output

For every driven adapter in the codebase, the team can answer:

- Which port does it implement?
- Which technology does it adapt?
- How does it translate domain vocabulary to the technology's vocabulary?
- How does it handle failures (retries, timeouts, mapping to error types)?
- Does it participate in transactions, and how?

If any of these has no answer, the adapter is improvising.
