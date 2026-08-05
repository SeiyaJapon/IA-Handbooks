# 10. User Preferences (Francisco)

Project conventions for Clean Architecture in Francisco Pérez's projects at Kintai.

## When clean is acceptable

The default backend architecture in this project is **hexagonal**, not clean. Clean is acceptable for a specific repo when:

- The forces match (rich domain, long lifetime, framework-independence valued).
- The team genuinely speaks Uncle Bob's vocabulary (Interactors, Boundaries, Presenters).
- The explicit Use Case structure (Input/Output Boundary + Interactor + Data) adds value.
- A different inward-dependency sibling (hexagonal, onion) is not already in use in the same monorepo.

Mixing siblings across the monorepo is rejected. One vocabulary per project.

## Vocabulary

When clean is in use, the codebase uses clean's vocabulary consistently:

- **Circles** (Entities, Use Cases, Interface Adapters, Frameworks & Drivers).
- **Use Case Interactor**, **Input Boundary**, **Output Boundary**, **Input Data**, **Output Data**, **View Model**.
- **Controller**, **Presenter**, **Gateway**.

The terms "port", "driving adapter", "driven adapter", "ring", "Domain Services ring" do not appear in code, comments, or documentation. They belong to hexagonal and onion respectively.

## Layout

A typical clean codebase in this project:

```
src/
├── entities/                       (innermost circle)
├── usecases/                       (Use Case circle: Interactors, Boundaries, Data, Gateway interfaces)
├── interface_adapters/             (Controllers, Presenters, Gateways)
└── frameworks_and_drivers/         (or main.ts + framework wiring)
```

Variants:

- Some teams prefer `application/` instead of `usecases/` and `infrastructure/` instead of `interface_adapters/`. Acceptable when consistent.
- The folder names are convention; the architecture is the dependency direction and the Use Case structure.

## Use Case structure

Every Use Case in production code has:

- **Interactor** named after the operation: `CreateInvoiceInteractor`, `ApprovePaymentInteractor`.
- **Input Boundary** interface: `CreateInvoiceInputBoundary`.
- **Output Boundary** interface: `CreateInvoiceOutputBoundary` (with multiple methods per outcome) : or the collapse is documented.
- **Input Data**: `CreateInvoiceInputData`.
- **Output Data**: `CreateInvoiceOutputData`.
- **Gateway interfaces** declared in the Use Cases circle: `InvoiceGateway`.

## Adapter naming

- Controllers: `<UseCase>Controller` (HTTP) or `<UseCase>CliCommand` (CLI).
- Presenters: `<UseCase><Format>Presenter` (e.g. `CreateInvoiceJsonPresenter`).
- Gateways: `<Tech><Aggregate>Gateway` (e.g. `PostgresInvoiceGateway`).

When there is only one implementation, the technology prefix may be dropped.

## Framework coupling

- Entities and Use Cases are framework-agnostic. No imports from any framework, ORM, or transport library.
- Interface Adapters know the framework's shape just enough to register Controllers and Gateways.
- Frameworks & Drivers contains the framework, the database engine, the broker.

DI is manual through constructors in Interactors. Framework decorators (`@Injectable`, `@CommandHandler`) live in Interface Adapters or in Frameworks & Drivers.

## Communication style

Avoid em dash in normal prose. When adding an aside or clarification, prefer parentheses or a separate sentence. Do not use em dash as a default punctuation device. Only keep em dash when quoting existing text or when explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the canonical names are `entities/`, `usecases/`, `interface_adapters/`, `frameworks_and_drivers/`, or framework-default approximations (`domain/`, `application/`, `infrastructure/`, with the same circles inside).
- Whether Output Boundary collapse (Interactor returns Output Data) is acceptable as a default for simpler use cases, or whether explicit Output Boundary is mandatory.
- Whether Gateway naming follows `<Tech><Aggregate>Gateway` strictly or uses framework-default approximations.
- Whether mono-repo coexistence of clean and hexagonal is ever allowed (default: no).

These are open. Do not assume; ask before treating any of them as a rule.
