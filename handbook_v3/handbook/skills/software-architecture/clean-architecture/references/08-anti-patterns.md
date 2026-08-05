# 08. Anti-patterns

The recurring failures of Clean Architecture in real codebases. Each is a pattern to recognise, name in clean's vocabulary, and reject during reviews.

## Direction violations (The Dependency Rule)

### Inner circle imports outer

`Entities` imports `Frameworks & Drivers` or `Interface Adapters`. `Use Cases` imports a concrete Gateway, Controller, or Presenter.

Why it fails: The Dependency Rule is broken. The inner circle is no longer testable or replaceable in isolation.

Fix: invert through a Boundary or Gateway interface. The inner circle declares; the outer implements.

### Use Case imports concrete Gateway

A Use Case Interactor imports `PostgresInvoiceGateway` instead of `InvoiceGateway` (the interface).

Fix: import the interface. The framework wires the concrete at composition time.

### ORM annotations on Entities

`@Entity`, `@Column`, `@OneToMany` on Entity classes.

Why it fails: Entities (the most stable code) depend on the ORM driver (the most volatile code). The direction is inverted.

Fix: separate persistence shape from the Entity. Persistence shape lives in the Gateway, in Interface Adapters.

### Framework decorators on Interactors

`@Injectable`, `@CommandHandler`, `@EventsHandler` on Use Case Interactors.

Why it fails: the Interactor depends on the framework. Replacing or upgrading the framework requires touching Use Cases.

Fix: wire the Interactor in the composition root (Frameworks & Drivers), without decorators in the Interactor itself.

## Use Case structure violations

### Interactor without Input Boundary

The Controller calls the Interactor's class directly, importing the concrete class.

Why it fails: Controller and Interactor are coupled. Substitution is impossible.

Fix: declare the Input Boundary; Controller depends on the interface; Interactor implements it.

### Output collapsed without justification

The Interactor returns a value; no Output Boundary, no Presenter.

Why it fails (sometimes): the system loses the ability to substitute the Presenter (test, multiple presentations).

Fix: when substitution is valuable, introduce the Output Boundary. When it is not (small systems, single presentation), document the deliberate collapse.

### One Interactor with many use cases

`InvoiceInteractor` with `create`, `cancel`, `refund`, `archive` methods.

Why it fails: each use case has different rules, different Boundaries, different test paths. Bundling them obscures the structure.

Fix: one Interactor per use case. `CreateInvoiceInteractor`, `CancelInvoiceInteractor`, etc.

### Input Data is the HTTP request

The Controller passes an `HttpRequest` directly to the Interactor.

Why it fails: the Interactor is coupled to HTTP. A second Controller (CLI, queue) cannot reuse it.

Fix: define `XxxInputData` in the Use Cases circle. The Controller builds it.

### Output Boundary with one method called for everything

The Output Boundary has `present(any)`. Different outcomes (success, not found, validation failed) all go through one method.

Why it fails: the Presenter cannot distinguish outcomes; HTTP status, View Model branching is implicit.

Fix: multiple methods on Output Boundary, one per outcome.

## Interface Adapters role confusion

### Controller builds the View Model

The Controller calls the Interactor (without Output Boundary) and formats the response itself.

Why it fails (when it matters): no Presenter. Substituting the output format requires changing the Controller.

Fix: when substitution matters, introduce Output Boundary + Presenter.

### Presenter calls a Use Case

A Presenter contains business logic or calls a Gateway directly.

Fix: Presenters format only. The Interactor orchestrates.

### Gateway with business rules

"If the row's status is X, decide Y."

Fix: business decisions belong in the Interactor or Entity.

### Generic Gateway

`Gateway<T>` with `find`, `save`, `delete`.

Fix: domain-named, per-Use-Case-need Gateway interfaces (`InvoiceGateway` with methods like `findOpenForCustomer(id)`).

### Gateway interface in Interface Adapters

The Gateway interface lives next to its implementation.

Why it fails: the Use Case must import from Interface Adapters to use the interface. Direction inverted.

Fix: the Gateway interface lives with the Use Case (its consumer), in the Use Cases circle.

## Frameworks & Drivers violations

### Framework code in Entities or Use Cases

Discussed above. Most damaging violation.

### Bootstrap that contains business decisions

The wiring layer (`main.ts`) decides what the system does, beyond hooking things up.

Fix: move the decisions inside Interactors. The bootstrap wires; it does not decide.

## Vocabulary violations

### "Ports" instead of "Boundaries"

Hexagonal vocabulary in a clean codebase.

Fix: rename. Boundaries are clean's term.

### "Driving / driven adapters" instead of "Controller / Presenter / Gateway"

Hexagonal vocabulary.

Fix: rename. Clean prescribes the three named roles.

### "Application Service" instead of "Interactor"

Hexagonal or DDD vocabulary in clean code.

Fix: rename. Clean's artefact is the Interactor.

### "Rings" instead of "circles"

Onion vocabulary.

Fix: rename. Clean uses circles.

## Architectural confusion

### "Clean = hexagonal"

The team uses both vocabularies interchangeably.

Fix: pick one. Migrate the rest.

### "Clean = DDD"

DDD is a design method. Clean is a structure. They are different decisions.

### "Clean = MVC"

MVC is a UI structure. Clean is a system architecture. MVC may live inside Interface Adapters.

### Folder-as-architecture

The team renames folders to `entities/`, `usecases/`, `interface_adapters/`, `frameworks/` without enforcing The Dependency Rule.

Fix: enforce the rule with tooling and review.

## Output

When reviewing a clean codebase, look for these patterns by name in clean's vocabulary. Direction violations and Use Case structure violations outrank vocabulary slips.
