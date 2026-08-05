# 02. The Three Layers

The classical layered architecture has three layers: presentation, business, data. Larger variants add layers, but the three-layer shape is the canonical one.

This document describes each layer, what it owns, what it does not own, and the most common naming variants.

## Presentation layer

The presentation layer handles input and output between the system and its callers (users, other systems).

### Responsibilities

- Receive requests in their transport format (HTTP, CLI arguments, GUI events).
- Validate input structure (presence, format, types).
- Translate the input into a call on the business layer.
- Translate the business layer's response back into the transport format.
- Render output (HTML, JSON, text, screen widgets).
- Handle transport-specific concerns: HTTP status codes, headers, exit codes.

### Non-responsibilities

- Business rules ("if total is over X, do Y"). Those go in the business layer.
- Data access (SQL queries, ORM operations). Those go in the data layer.
- Storage decisions.

### Common names

- **Presentation layer.**
- **UI layer.**
- **Web layer** (when HTTP is the only transport).
- **Controllers** (when MVC is used inside).

### Examples of contents

- HTTP controllers.
- View templates.
- Request/response DTOs.
- Input validators (structural only).
- Authentication middleware.
- API documentation generators.

## Business layer

The business layer holds the rules and orchestration of the system.

### Responsibilities

- Implement the rules of the business: what is allowed, what is calculated, what triggers what.
- Orchestrate operations that span multiple data accesses.
- Enforce invariants (in classical layered, these often leak across the layer; in stricter variants, they are encapsulated in domain objects).
- Translate from data-layer types to business types when possible (in stricter variants).
- Decide transactions.

### Non-responsibilities

- Transport details (HTTP, CLI). Those are the presentation layer's job.
- Storage details (SQL, ORM specifics). Those are the data layer's job.
- Framework lifecycle (request handling, dependency injection wiring at the top level).

### Common names

- **Business layer.**
- **Service layer.**
- **Application layer.**
- **Domain layer** (in DDD-flavoured layered).

### Examples of contents

- Service classes (`OrderService`, `ScenarioService`).
- Business rules (validators, calculators, evaluators).
- Workflow orchestrators.
- Domain entities (in stricter variants).
- Transaction management.

## Data layer

The data layer handles persistence: how state is stored and retrieved.

### Responsibilities

- Connect to the storage system (database, file system, external API used as storage).
- Issue queries, mutations, transactions in the storage's vocabulary.
- Map between storage types (rows, documents) and business types when possible.
- Handle storage-specific concerns: connection pooling, transactions, optimistic locking, indexing hints.

### Non-responsibilities

- Business rules. The data layer does not decide what is allowed; it executes what the business layer asks.
- Transport. The data layer does not know about HTTP or CLI.
- Validation beyond storage constraints.

### Common names

- **Data layer.**
- **Persistence layer.**
- **DAO (Data Access Object) layer.**
- **Repository layer** (when the Repository pattern is used).
- **Integration layer** (when calls to external systems are also here).

### Examples of contents

- Repositories or DAOs.
- ORM configuration and mappings.
- Database connection management.
- Query builders.
- Migration scripts.

## Variations on the three-layer shape

Real systems often add layers between the canonical three. Some common ones:

### Service layer (between presentation and business)

A thin layer that orchestrates business calls and shapes the response for the presentation. Sometimes called "application services" in DDD-flavoured layered.

### Integration layer (next to data)

Calls to external systems (third-party APIs, legacy systems) live in their own layer instead of in data. The data layer handles only internal storage; integration handles external systems.

### Domain layer (inside business)

In DDD-flavoured layered, the business layer is split into domain (entities, value objects, domain services) and application (orchestration, use cases). This is closer to hexagonal in shape but still classical layered in dependency direction.

### Infrastructure layer (under data)

Cross-cutting concerns (logging, configuration, security primitives) live in their own layer below data. All other layers can call into infrastructure.

## How layers interact

The interaction is strictly downward:

```
Presentation
    |
    v
Business
    |
    v
Data
```

- Presentation calls Business.
- Business calls Data.
- Presentation does NOT call Data directly.
- Data does NOT call Business or Presentation.

This direction is the defining property of layered. It distinguishes layered from hexagonal (which inverts to inward), from MVC (which is intra-presentation), and from microservices (which is across-services).

## What lives where: placement table

| Concept | Layer | Notes |
|---|---|---|
| HTTP controller | Presentation | Driving entry point. |
| Request/response DTO | Presentation | Transport-shaped data. |
| Input validator (structure) | Presentation | Format only, not business. |
| Service class | Business | Orchestration and rules. |
| Domain entity | Business (or Domain sublayer) | Behaviour and state. |
| Workflow / use case | Business | Cross-class orchestration. |
| Repository or DAO | Data | Persistence interface. |
| ORM mapping | Data | Database-shaped configuration. |
| External API client | Data or Integration | Depends on the variant. |
| Logger / config | Cross-cutting (or Infrastructure) | Used by all layers. |

## Output

For a layered architecture to be well defined, the team can answer:

- What are the layers in this system?
- What does each layer own and not own?
- Where does each class belong, and why?
- Are there variants (service layer, integration, domain, infrastructure)?

Without these, the architecture exists in name only, and classes drift into the wrong layers.
