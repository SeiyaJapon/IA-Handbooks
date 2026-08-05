# 04. Driving Adapters

A **driving adapter** is the entry point through which an external actor reaches the application core. It receives input in the actor's protocol (HTTP request, CLI arguments, queue message, scheduled trigger), translates that input into a call on a **driving port**, and translates the result back to the actor.

Driving adapters live in `infrastructure/`. They are thin, transport-aware, and contain no business rules.

## Common kinds of driving adapters

### HTTP controller

Receives HTTP requests and calls a driving port.

Responsibilities:

- Deserialise the HTTP request (path, query, body, headers) into a command, query, or method arguments.
- Call the driving port.
- Translate the result into an HTTP response (status code, body, headers).
- Translate exceptions into HTTP error responses.

Non-responsibilities:

- Deciding whether the operation is allowed (that is the domain).
- Computing anything beyond format translation.
- Storing or retrieving data directly.

Typical placement: `infrastructure/http/<UseCase>.controller.ts`.

### CLI handler

Receives a CLI invocation (subcommand, flags, arguments) and calls a driving port.

Responsibilities:

- Parse arguments into a command, query, or method arguments.
- Call the driving port.
- Print results to stdout.
- Map errors to exit codes and messages on stderr.

Typical placement: `infrastructure/cli/<UseCase>.command.ts`.

### Queue consumer

Receives messages from a queue (SQS, RabbitMQ, Kafka, SNS-to-SQS) and calls a driving port.

Responsibilities:

- Deserialise the message payload into a command or method arguments.
- Call the driving port.
- Acknowledge the message on success.
- Handle failures: retry, dead-letter, requeue, depending on the broker contract.

Specific to this project: the term **consumer** is used for SQS-based workers (and equivalents). Other terms (`worker`, `listener`, `subscriber`) are not the canonical name.

Typical placement: `infrastructure/messaging/<Event>.consumer.ts`.

### Scheduler / cron entry point

Triggered by a scheduler (cron, EventBridge schedule, scheduled lambda) and calls a driving port.

Responsibilities:

- Read schedule configuration if relevant (current time, last run).
- Call the driving port (typically a use case that performs the periodic operation).
- Log success or failure.

Typical placement: `infrastructure/schedulers/<UseCase>.schedule.ts`.

### Event handler / subscriber (cross-context)

When a context subscribes to integration events from another context, the subscriber is a driving adapter for the consuming context.

- Reads the integration event.
- Translates it into a command in the consuming context's vocabulary (often through an ACL).
- Calls the driving port.

This is distinct from a **driven event bus adapter** that publishes events outward; that is a driven adapter (see `05-driven-adapters.md`).

## Properties of a good driving adapter

- **Thin.** Few lines of meaningful logic. Most of its body is translation.
- **One operation per adapter (typical).** A controller exposes one endpoint that calls one driving port. A CLI command performs one action. A consumer handles one message type.
- **Transport-aware.** Knows the protocol (HTTP status codes, queue acknowledgements, CLI exit codes).
- **Domain-blind.** Does not know what the operation does, only how to deliver input and output.
- **Failure-aware.** Knows how its protocol communicates failure (HTTP 4xx/5xx, exit codes, requeue or DLQ).

## Translating input

The driving adapter translates from the protocol's vocabulary to the core's vocabulary.

- The HTTP request body is JSON; the driving port expects a `CreateScenarioCommand` instance.
- The CLI arguments are strings; the driving port expects typed values.
- The queue message is a serialised event; the driving port expects an in-memory object.

Validation belongs to the driving adapter only at the **structural** level (presence of required fields, format of values). Business validation (does the referenced entity exist, is the operation allowed) belongs to the application or domain layer.

## Translating output

The driving adapter translates from the core's result back to the protocol.

- A successful command may return nothing or a small acknowledgement; the controller maps that to HTTP 201 with a Location header, or HTTP 204.
- A query returns a result; the controller maps that to JSON.
- A queue consumer typically returns nothing; the consumer acknowledges the message.

## Translating errors

The driving adapter is the place where domain errors meet the protocol.

- A `DomainError` raised by the core becomes an HTTP 400 / 409 / 422 with a structured body.
- An `ApplicationError` (not found, conflict) becomes its mapped HTTP status.
- An `InfrastructureError` becomes HTTP 500 or a retryable response, depending on policy.

The mapping is centralised when possible (an error filter in a framework, a shared error formatter) so that every controller does not redo it. The mapping is also consistent across all driving adapters: a `NotFound` is the same kind of failure whether reached over HTTP or over the CLI.

## Driving adapters and frameworks

Driving adapters typically use the framework's idioms:

- HTTP controllers in NestJS use `@Controller`, `@Get`, `@Post`. That is allowed because controllers live in `infrastructure/`.
- CLI handlers may use a CLI framework (oclif, commander).
- Queue consumers may use a messaging library (sqs-consumer, KafkaJS).

The framework coupling is contained to `infrastructure/`. The driving port that the adapter calls is a plain interface, framework-free.

## Anti-patterns

- **Driving adapter that contains business rules.** "If the user role is X, deny the request." That decision belongs in the domain or application layer. The adapter only forwards.
- **Driving adapter that calls a driven adapter directly.** "Just read from the database here." This bypasses the core. The adapter must call a driving port; the driving port (a use case) is the one that uses driven ports.
- **Driving adapter that owns the transaction.** Transactions are decided by the application layer (the use case). The driving adapter does not begin or commit.
- **Driving adapter that does composition of operations.** "First call use case A, then call use case B." That composition is itself a use case; create one and call it from the adapter.
- **Driving adapter that mutates state directly.** Any state change must go through a driving port.
- **God controller.** A single class with twelve endpoints calling ten different operations. Split per operation.
- **Driving adapter that exposes the core's internal types as the wire format.** A `Scenario` aggregate is not a JSON response. The adapter constructs a response DTO from the result of the use case.

## Output

For every driving adapter in the codebase, the team can answer:

- Which protocol does it serve (HTTP, CLI, queue, schedule)?
- Which driving port does it call?
- How does it translate input?
- How does it translate output?
- How does it translate errors?
- What is its failure behaviour for the protocol (status codes, retries, dead-letter)?

If any of these has no answer, the adapter is improvising.
