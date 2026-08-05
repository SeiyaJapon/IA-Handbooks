# 06. Frameworks and Drivers

The outermost circle. Holds the framework, the database engine, the web server, the broker, the devices. The most volatile circle, kept at the edge so it can be replaced without rewriting the inner circles.

This is clean's vocabulary: **Frameworks & Drivers**. Hexagonal calls this region "infrastructure" without splitting it; onion calls it the "External Layer". Clean specifies that what lives here is "frameworks" and "drivers": code that exists because of a specific external choice.

## What lives here

- **Framework code.** NestJS modules, Spring's bootstrap, Laravel service providers.
- **Database drivers.** The Postgres client, the MongoDB driver, the Redis library.
- **HTTP server.** Express, Fastify, the framework's HTTP module.
- **Broker clients.** SQS, Kafka, RabbitMQ libraries.
- **DI configuration.** Where Interface Adapters get registered into the framework.
- **Bootstrap and main.** The application entry point that wires everything.
- **Glue code.** The minimal code that hooks Interface Adapters into the framework's lifecycle.

## What does not live here

- Business rules.
- Use Case Interactors.
- Entities.
- Adapters logic (Controllers, Presenters, Gateways live one circle inward in Interface Adapters).
- Boundary interfaces. Those are owned by Use Cases.

## The framework as glue

In clean, the framework's job is to wire Interface Adapters into the runtime. It registers Controllers with HTTP routes, instantiates Gateways with their dependencies, starts the HTTP server, connects to the database, etc.

The framework does not know the business. The business code (Entities and Use Cases) is unaware of the framework. The Interface Adapters know the framework's shape (decorators, lifecycle hooks) just enough to register themselves; they do not depend on the framework as a behavioural collaborator.

## Replacing the framework

Because dependencies point inward and the framework is at the edge:

- Replacing the framework requires rewriting the registration layer (the wiring) and possibly the Controller decorators.
- It does not require rewriting Use Case Interactors, Entities, or Boundary interfaces.
- The cost is bounded to the outermost circle.

This is the operational benefit of clean: stable code stays untouched when the framework changes.

## Anti-patterns

- **Framework code in the inner circles.** A Use Case Interactor that imports `@nestjs/common` has dragged the framework into the Use Cases circle. Replacing the framework now means rewriting Use Cases.
- **ORM annotations on Entities.** The ORM (a driver) couples its types into Entities. Worst case: changing the ORM changes Entity files that should be the most stable.
- **The framework as the architecture.** "We use NestJS, so we are clean." NestJS is in the outermost circle; the architecture is the rules enforced on top.
- **Boundary interfaces in Frameworks & Drivers.** They belong with the Use Cases that declare them.
- **Frameworks & Drivers as a dumping ground.** Code that does not fit elsewhere is placed in the outermost circle. It should be in Interface Adapters or further inward.
- **Bootstrap that contains business decisions.** The bootstrap wires; it does not decide. Business decisions belong inside Interactors.

## Output

For the Frameworks & Drivers circle, the team can answer:

- What lives here, and what would change if we replaced the framework or the database?
- Is anything that should be inward (business logic, Interactor logic, Boundary interfaces) leaking into this circle?
- Is the framework wiring concentrated in a known location, or scattered?

Without these, the framework's volatility leaks into the rest of the architecture.
