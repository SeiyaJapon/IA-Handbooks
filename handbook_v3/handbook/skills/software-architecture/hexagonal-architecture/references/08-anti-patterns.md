# 08. Anti-patterns

The recurring failures of hexagonal architecture in real codebases. Each of these is a pattern to recognise, name, and reject during reviews.

## Layout anti-patterns

### `presentation/` as a layer

HTTP controllers, GraphQL resolvers, CLI handlers, queue consumers placed in a top-level `presentation/` folder.

- Why it fails: hexagonal has three layers (`domain/`, `application/`, `infrastructure/`). `presentation/` is not one of them. Delivery mechanisms are driving adapters; they belong in `infrastructure/`.
- Symptom: the team treats `presentation/` as a fourth layer with its own dependency rules.
- Fix: move the contents to `infrastructure/http/`, `infrastructure/cli/`, `infrastructure/messaging/` as appropriate. Delete the `presentation/` folder.

### Framework modules at the root

`PrismaModule`, `AuthModule`, `DatabricksModule` sitting at the root of `src/` next to `domain/`, `application/`, `infrastructure/`.

- Why it fails: framework module files are infrastructure. Placing them at the root mixes two organisational axes (layers and feature folders) in the same tree.
- Symptom: the root of `src/` has both layer folders and unrelated feature folders.
- Fix: move framework modules into `infrastructure/` (layer-first) or into the bounded context's `infrastructure/` (context-first).

### Mixed organisational axes

Some folders at the root are layers (`domain/`, `application/`, `infrastructure/`), other folders at the root are bounded contexts or features (`auth/`, `product/`, `pipeline/`).

- Why it fails: hexagonal expects one organising axis per project. Two axes in the same tree make the dependency direction ambiguous.
- Symptom: contributors are unsure where a new file belongs.
- Fix: pick one axis (bounded-context-first or layer-first) and migrate the rest.

### Empty layers with decorative READMEs

`domain/` and `application/` exist as folders containing only a README explaining what should go there.

- Why it fails: the folder suggests structure that does not exist. Reviewers may treat the project as more layered than it actually is.
- Symptom: a folder with one `.md` file and no code.
- Fix: delete the empty layers. Recreate them when there is content.

### `core/` next to `domain/`

A folder named `core/` exists alongside `domain/`, with overlapping responsibilities.

- Why it fails: `domain/` is the core. A second folder with the same role is duplication and dilutes the meaning of both.
- Fix: pick one name (`domain/`) and consolidate.

## Dependency direction anti-patterns

### Domain imports infrastructure

A class in `domain/` imports from `infrastructure/`, an SDK, an ORM, an HTTP library, or a framework.

- Why it fails: the dependency rule is broken. The domain is no longer testable without that dependency.
- Symptom: `import { PrismaClient } from "@prisma/client"` inside a `domain/` file.
- Fix: invert the dependency. Define a port in the core; have the infrastructure implement it.

### Application imports concrete adapter

A use case in `application/` imports a concrete adapter (a specific repository implementation, a specific external client) instead of depending on the port.

- Why it fails: the use case is now tied to that adapter. Replacing the adapter requires changing the use case.
- Symptom: `import { PostgresOrderRepository } from "../infrastructure/..."` inside an `application/` file.
- Fix: import the port (the interface) and inject the concrete adapter at composition time.

### Domain depends on the framework

Domain entities use framework decorators (`@Entity`, `@Injectable`), framework types, or framework-specific lifecycle hooks.

- Why it fails: the domain is coupled to the framework. Framework upgrades, replacements, or testing without the framework all become hard.
- Symptom: a class in `domain/` annotated with `@Entity()` from an ORM.
- Fix: remove the framework coupling from the domain. Map between the domain entity and the persistence type inside the repository adapter.

### Port defined in `infrastructure/`

The interface (port) lives next to its implementation in `infrastructure/`. The application or domain imports from `infrastructure/` to use the port.

- Why it fails: the port is supposed to be owned by the core. If it lives outside, the core depends on infrastructure.
- Symptom: `import { OrderRepository } from "../infrastructure/persistence/..."` inside `application/`.
- Fix: move the interface into `application/` or `domain/`. The implementation stays in `infrastructure/`.

## Adapter anti-patterns

### Driving adapter contains business rules

An HTTP controller, CLI handler, or queue consumer contains decisions about whether the operation is allowed, what value to compute, or how to respond based on business state.

- Why it fails: the rule is in the wrong place. A second driving adapter for the same operation has to duplicate the rule (or, worse, behave differently).
- Symptom: an `if` statement in a controller that decides whether the operation should proceed based on user role, account status, or business attributes.
- Fix: move the decision into the application service or the domain. The adapter only translates input and output.

### Driving adapter calls a driven adapter directly

An HTTP controller reads from the database directly, bypassing the use case.

- Why it fails: the use case is the contract. Bypassing it means there is no single place that defines what an operation does.
- Symptom: a controller that imports a repository.
- Fix: make the controller call the use case. The use case uses the repository.

### Driven adapter that depends on another driven adapter

A repository imports an external client. An external client imports a repository.

- Why it fails: adapters are independent units. Composition is the application's job.
- Symptom: `import { ProductRepository } from "../persistence/..."` inside an external client adapter.
- Fix: move the composition into a use case. Have the use case call both adapters.

### Adapter exposes the SDK

A repository's interface mirrors the ORM's API. An external client exposes raw HTTP methods.

- Why it fails: the SDK has leaked through the port. Replacing the SDK now requires changing the port and every caller.
- Symptom: a port method named `executeQuery(sql: string)` or `httpGet(url: string)`.
- Fix: redesign the port in the core's vocabulary. The adapter does the translation.

### Generic adapter

A `Repository<T>` or `Client<T>` that adapts a category of entities or services with generics.

- Why it fails: adapters are specific. A generic adapter is shaped by the persistence model, not by the domain.
- Symptom: methods named `findAll`, `findOne`, `save`, `delete` on a generic interface.
- Fix: define one port per aggregate or capability, with domain-language methods.

### Adapter that throws SDK exceptions

The adapter lets the SDK's exceptions propagate to the application service.

- Why it fails: the application service now has to know about SDK error types, which couples the core to the SDK.
- Symptom: a `try/catch` in a use case that catches `PrismaClientKnownRequestError` or `AxiosError`.
- Fix: catch in the adapter, translate to the core's error types, throw those.

## Application layer anti-patterns

### Use case that contains business rules

The use case decides whether an operation is allowed based on business attributes, computes derived values, or enforces invariants.

- Why it fails: business rules belong in the domain. A use case that contains them has absorbed work that should be in the aggregate or the domain service.
- Symptom: an `if` in a use case that compares aggregate fields and decides what to do.
- Fix: push the decision into the aggregate. The use case calls a method on the aggregate; the aggregate enforces the rule.

### Use case that imports infrastructure

The use case calls a concrete adapter, an SDK, or a database client directly.

- Why it fails: the use case is no longer testable without infrastructure.
- Symptom: `import { PrismaClient } from "@prisma/client"` inside a use case.
- Fix: replace with a port; inject the adapter at composition.

### Use case that knows about the transport

The use case returns HTTP status codes, sets headers, or formats responses for a specific transport.

- Why it fails: the use case is tied to that transport. A second driving adapter (CLI, queue) cannot reuse it without rework.
- Symptom: a use case method that returns `{ status: 201, body: ... }`.
- Fix: the use case returns a domain or application result. The driving adapter translates to the transport.

### God use case

A single class with many methods, each one a separate operation.

- Why it fails: hides what the application does. A single class with twelve methods is twelve use cases pretending to be one.
- Symptom: `OrderService` with `createOrder`, `cancelOrder`, `applyDiscount`, `markPaid`, etc.
- Fix: one use case per operation, named after the operation.

### Validation everywhere

Structural validation in the use case, domain validation in the controller, application validation in the repository.

- Why it fails: each layer ends up doing the others' job. Bugs duplicate or fall between the cracks.
- Symptom: the same kind of check in two places, or a missing check because everyone assumed it was elsewhere.
- Fix: structural validation in the driving adapter or use case parsing; application validation in the use case (existence, references); domain validation in the aggregate (rules).

## Architectural confusion anti-patterns

### "Hexagonal because we have three folders"

A project has `domain/`, `application/`, `infrastructure/` and the team claims to be hexagonal, but the dependency rule is not enforced.

- Why it fails: hexagonal is the dependency rule, not the folder names. Three folders with arbitrary imports is not hexagonal.
- Symptom: `domain/` imports from `infrastructure/` somewhere, and the team has not noticed.
- Fix: enforce the rule. Tooling, code review, tests that fail when the direction is broken.

### "Hexagonal means DDD"

The team uses "hexagonal" and "DDD" interchangeably.

- Why it fails: they are different decisions. Confusing them makes both harder to apply.
- Symptom: a project that is hexagonal but has an anemic core; a project that has DDD aggregates but framework code in the domain.
- Fix: separate the concerns. Use the hexagonal skill for the architecture, the DDD skill for the design.

### "We're hexagonal, so we don't need MVC"

The team has rejected MVC without realising MVC is a UI pattern, not an architecture.

- Why it fails: rejecting MVC inside an HTTP adapter means rebuilding worse versions of the same patterns by hand.
- Fix: MVC is fine inside the HTTP driving adapter. It does not contradict hexagonal.

### "Hexagonal means microservices"

The team conflates hexagonal with deployment topology.

- Why it fails: hexagonal is internal to the application. A monolith can be hexagonal; a microservice can be procedural.
- Fix: separate decisions. Hexagonal is about isolating the core; microservices is about how the system is deployed.

## Output

When reviewing a hexagonal codebase, look for these patterns by name. Each is a finding. Cluster them in the review report by category (layout, dependency, adapter, application, confusion). Findings in the layout and dependency categories take priority because they invalidate the architecture; findings in the other categories are localised problems.
