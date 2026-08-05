# 03. The Dependency Rule

The single most important rule in Clean Architecture, named explicitly by Uncle Bob:

> **"Source code dependencies can only point inwards."**
> (Robert C. Martin)

This is the rule that makes a project clean. Without it, the four circles are decoration.

## Statement

For any pair of circles A and B, if A is **inside** B (closer to the centre), then code in B may depend on code in A, but **code in A must not depend on code in B**.

Concretely:

- Entities depend on nothing.
- Use Cases depend on Entities.
- Interface Adapters depend on Use Cases and Entities.
- Frameworks & Drivers depend on Interface Adapters (and indirectly on the inner circles).

## What "depend" means

In source code terms, any of the following from circle A to circle B counts as a dependency:

- `import`, `using`, `include`, `require`.
- Inheritance, interface implementation, generic parameters.
- Return types, parameter types, exception types.
- Annotations, decorators (when they couple to a specific framework).

The rule applies to **compile-time** (or build-time) dependencies. Runtime calls can flow outward; source-code dependencies cannot.

## Inversion through Boundaries

Clean preserves the rule through **Boundaries**, which is clean's vocabulary for dependency inversion.

- The Use Case declares an **Input Boundary**: an interface implemented by the Use Case Interactor itself, called by the Controller.
- The Use Case declares an **Output Boundary**: an interface implemented by the Presenter, called by the Interactor to deliver the result.
- The Use Case declares **Gateway interfaces**: implemented by Gateways in Interface Adapters, called by the Interactor.

```
[Controller] ---calls---> [Input Boundary] <---implements--- [Interactor]
                                                                  |
                                                                  | calls
                                                                  v
                                                            [Gateway interface]
                                                                  ^
                                                                  | implements
                                                            [Gateway concrete]

[Interactor] ---calls---> [Output Boundary] <---implements--- [Presenter]
```

All arrows of source-code dependency point inward. Calls at runtime can go outward (the Interactor calls the Gateway implementation, indirectly), but the import direction is inward.

## Boundary types in clean

Clean names two kinds of Boundaries:

- **Use Case Boundary** (Input Boundary + Output Boundary): the contract between a Use Case and the outer circle for one specific operation.
- **Gateway** interface: the contract between a Use Case and an external dependency (data store, external service).

This is more granular than hexagonal's "ports" (driving/driven) and onion's "interfaces declared by inner rings". Clean prescribes the Boundary structure for use cases.

## Examples

### Allowed

- `usecases/CreateInvoiceInteractor.ts` imports `entities/Invoice.ts`. Use Cases → Entities. ✓
- `infrastructure/postgres/PostgresInvoiceGateway.ts` imports `usecases/InvoiceGateway.ts` (the interface). Interface Adapters → Use Cases. ✓
- `infrastructure/web/CreateInvoiceController.ts` imports `usecases/CreateInvoiceInputBoundary.ts`. Interface Adapters → Use Cases. ✓
- `infrastructure/web/CreateInvoicePresenter.ts` implements `usecases/CreateInvoiceOutputBoundary.ts`. Interface Adapters implements Use Case interface. ✓
- `main.ts` wires everything. Frameworks & Drivers → Interface Adapters. ✓

### Forbidden

- `entities/Invoice.ts` imports `infrastructure/...`. Entities → Frameworks. ✗
- `usecases/CreateInvoiceInteractor.ts` imports `infrastructure/postgres/PostgresInvoiceGateway.ts`. Use Cases → Interface Adapters (concrete). ✗
- `entities/Invoice.ts` has `@Entity` annotation from an ORM. Entities → Frameworks. ✗
- `usecases/CreateInvoiceInteractor.ts` has `@CommandHandler` decorator. Use Cases → Frameworks. ✗

## How violations happen

- **Convenience imports.** "It is just one method." Once allowed, the door is open.
- **ORM annotations on Entities.** The ORM's types couple to Entities.
- **Framework decorators on Interactors.** The framework's types couple to Use Cases.
- **Tests that import internals from outer circles to avoid setup.** Acceptable in tests if isolated; not acceptable if production code follows.
- **Treating Gateway interfaces as if they belonged to Interface Adapters.** The interface lives with the Use Case (its consumer); the implementation lives in Interface Adapters.

## Enforcement

The Dependency Rule is enforced through:

- **Lint rules** that forbid imports across the wrong direction.
- **Dependency graph tools** that visualise import direction.
- **Architecture tests** that fail when imports cross the rule.
- **Code review** for what tooling cannot catch.
- **No exceptions for "just this one time".**

A documented rule that is not enforced is a slogan.

## Why the inward direction

The benefit is **isolation of stable code from volatile code**. Entities and Use Cases are the most stable; Frameworks & Drivers are the most volatile. The rule ensures the stable code is not shaped by the volatile code.

Reverse the direction (as classical layered does, with the data layer at the bottom and types leaking upward) and the stable code becomes shaped by the volatile code. Database schema changes ripple into business logic. Framework upgrades require domain rewrites.

## Output

For The Dependency Rule to be sound, the team can answer:

- For every import, is the direction valid?
- Are Boundaries used to invert dependencies that would otherwise cross?
- Is the rule enforced by tooling, by review, or both?
- What pragmatic exceptions are documented (if any)?

Without enforcement, "we are clean" is a label, not a property.
