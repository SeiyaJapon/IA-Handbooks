# 02. The Four Rings

Onion Architecture organises the system in **four concentric rings** around the Domain Model. From innermost to outermost: Domain Model, Domain Services, Application Services, External Layer. Each ring has a specific responsibility.

The four rings are onion's prescribed shape. Hexagonal does not have rings; clean has circles with different partitioning. The vocabulary is not interchangeable.

## Domain Model (innermost ring)

Holds **entities and value objects**. The data structures and per-entity behaviour of the domain.

- Most stable ring.
- No imports from any outer ring.
- No framework, no ORM, no HTTP, no SDK.
- Pure model.

What lives here:

- Entity classes with identity and per-entity behaviour.
- Value objects.
- Domain errors raised by entities and value objects when invariants on a single entity are violated.

What does NOT live here:

- Cross-entity logic. That is the Domain Services ring.
- Use case orchestration. That is the Application Services ring.
- Anything related to persistence, transport, framework.

Examples (Palermo's literature uses C#/.NET examples; the principle is language-agnostic):

- `Customer` entity (identity, per-customer behaviour).
- `Money` value object.
- `Invoice` entity (per-invoice behaviour, e.g. add a line, mark paid).

## Domain Services ring

Holds **domain behaviour that does not belong to a single entity**. Cross-entity logic, intrinsic to the business.

- Less common than Domain Model.
- Imports from Domain Model.
- No framework, no infrastructure.
- Stateless or domain-meaningful state only.

What lives here:

- Domain Services that coordinate multiple Domain Model objects.
- Domain Services that compute values from multiple entities.
- Domain Services that enforce invariants spanning entities.

Examples:

- `RiskScoreEvaluator` (evaluates a scenario combining customer, history, product).
- `EligibilityChecker` (decides whether a customer is eligible for a product, given multiple entities).
- `InvoiceNumberGenerator` (issues numbers following enterprise rules, possibly stateful).

What does NOT live here:

- Behaviour that belongs to one entity (move it inside the entity in Domain Model).
- Orchestration of use cases (Application Services).
- Generic services or utilities (move to External Layer or a utility module).

The **Domain Services ring is onion's distinguishing feature**. If it would be empty, onion's specific contribution is absent; hexagonal or clean is a lighter fit.

## Application Services ring

Holds **use cases**: orchestration of Domain Model and Domain Services to satisfy application operations.

- One Application Service per use case.
- Imports from Domain Services and Domain Model.
- Declares interfaces for what it needs from the External Layer.
- No framework imports.

What lives here:

- Application Service classes (one per business operation).
- Interfaces for external collaborators (repository-like contracts, external service contracts, event bus, time, identity).
- Command, query, and result data structures (when used).
- Application errors (not-found, conflict, duplicate).

Examples:

- `EvaluateRiskUseCase.execute(scenarioId)`: loads scenario, calls `RiskScoreEvaluator` (Domain Service), persists result, dispatches event.
- `IssueInvoiceUseCase.execute(...)`: loads customer, creates invoice, calls `InvoiceNumberGenerator` (Domain Service), persists.

What does NOT live here:

- Intrinsic domain logic (push it into Domain Services or entities).
- Concrete adapter implementations (those are External Layer).
- Framework code.

## External Layer (outermost ring)

Holds **everything outside the domain**: persistence, transport, framework, UI, external services.

- Most volatile ring.
- Imports inward freely.
- Implements interfaces declared by Application Services.

What lives here:

- Repositories (concrete persistence implementations).
- External service clients.
- Event bus implementations.
- HTTP controllers, CLI handlers, queue consumers (driving entries; onion does not split them by name as clean does).
- Framework wiring (DI configuration, application bootstrap).
- UI code (in onion-style desktop apps).

Onion does NOT split this ring into Controller / Presenter / Gateway as clean does. They all live in the External Layer; the team may organise them by sub-folder if useful, but onion does not prescribe the role names.

## Why four rings (not three, not two)

The Domain Services ring is what makes the four-ring shape onion-distinctive:

- Hexagonal has two regions (inside the hexagon, outside the hexagon).
- Clean has four circles but folds Domain Services into Entities or Use Cases.
- Onion explicitly names Domain Services as its own ring.

The argument: Domain Services and Application Services have different volatilities. Domain Services change when business rules change; Application Services change when the application's operations change. Separating them is useful when both kinds of change happen at different rates.

When the domain has no significant cross-entity logic, the Domain Services ring may be empty or thin. In that case, onion is heavier than necessary; hexagonal or clean fit better.

## Placement

| Concept | Ring |
|---|---|
| `Money` value object | Domain Model |
| `Invoice` entity | Domain Model |
| `RiskScoreEvaluator` (cross-entity) | Domain Services |
| `EligibilityChecker` (cross-entity) | Domain Services |
| `EvaluateRiskUseCase` | Application Services |
| `InvoiceRepository` interface | Application Services |
| `RiskEventBus` interface | Application Services |
| `PostgresInvoiceRepository` (concrete) | External Layer |
| HTTP controller | External Layer |
| Framework module | External Layer |

## Output

For each piece of the codebase, the team can answer:

- Which ring does it belong to?
- For Domain Model: is it an entity (with identity) or a value object?
- For Domain Services: what cross-entity intrinsic logic does it encapsulate?
- For Application Services: what use case does it orchestrate?
- For External Layer: which interface does it implement, or which transport does it adapt?

Without this clarity, the four rings exist in folder names only.
