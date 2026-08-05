# 02. The Four Circles

Clean Architecture organises the system in **four concentric circles**. From innermost to outermost: Entities, Use Cases, Interface Adapters, Frameworks & Drivers. Each circle has a specific responsibility and a specific volatility.

The four circles are clean's prescribed shape. Hexagonal does not have circles; onion has rings. The vocabulary is not interchangeable.

## Entities (innermost)

Holds **enterprise-wide business rules**: rules that exist regardless of the current application.

- Most stable circle.
- No imports from any outer circle.
- No framework, no ORM, no HTTP, no SDK.
- Pure model.

What lives here:

- Entity classes with identity and behaviour.
- Value objects.
- Enterprise-wide business policies.
- Domain errors raised by entities and value objects.

Examples (Uncle Bob's): `Money` (how money works regardless of application), `Customer` (identity, equality, enterprise rules), `Invoice` (numbering rules, enterprise format).

What does NOT live here:

- Application-specific orchestration. That is the Use Case circle.
- Persistence concerns. That is Interface Adapters / Frameworks & Drivers.
- Any concept that exists only because of this specific application.

## Use Cases

Holds **application-specific business rules**. What this particular application does.

- Volatility: medium. Changes when the application's behaviour changes.
- Imports from Entities.
- Declares **Input Boundaries** and **Output Boundaries**.
- Declares **Gateway interfaces** for outside collaborators.
- No framework imports.

What lives here:

- **Use Case Interactors** : one class per business operation, implementing the Input Boundary.
- **Input Boundary** interfaces : the contract Controllers call.
- **Output Boundary** interfaces : the contract Presenters implement.
- **Input Data** structures : plain data carrying the request.
- **Output Data** structures : plain data carrying the result.
- **Gateway interfaces** : what the Interactor needs from the outside (a Repository-like contract, an external service contract).

The full Use Case structure is detailed in `references/04-use-case-structure.md`.

## Interface Adapters

Translates between the inner circles' format and outer formats. Clean splits this circle into **three named roles**.

### Controller

Driving entry. Receives input from outside (HTTP request, CLI args, queue message), translates into Input Data, calls the Input Boundary.

- Knows the transport (HTTP, CLI, queue).
- Translates input.
- Does not contain business rules.

### Presenter

Output side. Implements the Output Boundary. The Interactor calls the Presenter (through the Output Boundary) to deliver the result. The Presenter formats the output for the outer circle (View Model, JSON, HTML).

- Knows the output format.
- Builds the **View Model** consumed by the View.
- Does not contain business rules.

### Gateway

Driven side. Implements the Gateway interfaces declared by the Use Cases. Talks to the database, external APIs, file system.

- Knows the technology (SQL, HTTP, SDK).
- Translates between domain types and the technology's vocabulary.
- Does not contain business rules.

The three roles are **distinct** in clean. Collapsing Controller and Presenter into a single object, or Gateway and Repository into a single class without distinguishing the contract from the implementation, weakens the architecture.

## Frameworks & Drivers (outermost)

The framework, the database engine, the web server, the broker, the devices. Most volatile circle.

- The framework wires everything together.
- The database engine, the message broker, the HTTP server live here.
- Imports from Interface Adapters.
- Easy to replace because the inner circles do not depend on it.

What lives here:

- The framework's bootstrap and configuration.
- Database connection setup.
- Message broker client.
- HTTP server.
- DI configuration.
- Glue code that wires Interface Adapters into the framework's lifecycle.

What does NOT live here:

- Anything that is not the framework or the driver.

## Volatility and isolation

The circles are ordered by volatility. Replacing the database (Frameworks & Drivers) does not affect Entities. Replacing the framework does not change Use Cases. The Dependency Rule keeps the inner circles isolated from the outer.

## Practical placement

| Concept | Circle |
|---|---|
| `Money` value object | Entities |
| `Invoice` entity | Entities |
| `CreateInvoiceInteractor` | Use Cases |
| `CreateInvoiceInputBoundary` | Use Cases |
| `CreateInvoiceOutputBoundary` | Use Cases |
| `CreateInvoiceInputData` | Use Cases |
| `CreateInvoiceOutputData` | Use Cases |
| `InvoiceGateway` interface | Use Cases |
| `CreateInvoiceController` | Interface Adapters |
| `CreateInvoicePresenter` | Interface Adapters |
| `InvoiceViewModel` | Interface Adapters |
| `PostgresInvoiceGateway` | Interface Adapters |
| `app.module.ts` (NestJS wiring) | Frameworks & Drivers |
| Postgres client library | Frameworks & Drivers |

## Output

For each piece of the codebase, the team can answer:

- Which circle?
- For Use Cases: which Boundary, which Data, which Interactor?
- For Interface Adapters: which role (Controller, Presenter, Gateway)?
- Does it respect The Dependency Rule?

Without this clarity, the four-circle structure exists in folder names only.
