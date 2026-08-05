# 04. Use Case Structure

This is the artefact most distinctive of clean. Hexagonal does not prescribe it; onion does not prescribe it. Clean does.

A Use Case in clean is not a class, not a method, not a service. It is a **structure of artefacts** with explicit roles.

## The full structure

For one use case (one business operation), clean prescribes:

- **Input Data** : a plain data structure carrying the request.
- **Input Boundary** : an interface with a single method, declared in the Use Cases circle, called by the Controller.
- **Use Case Interactor** : the class that implements the Input Boundary. Holds the application-specific business rules.
- **Output Data** : a plain data structure carrying the result.
- **Output Boundary** : an interface declared in the Use Cases circle, implemented by the Presenter, called by the Interactor to deliver Output Data.
- **Gateway interfaces** : declared in the Use Cases circle, implemented by Gateways in Interface Adapters.

That is six artefacts per use case (or five, if Gateway is shared across use cases).

## Why six artefacts

Each artefact has a reason:

- **Input Data:** decouples the request shape from the transport's shape (HTTP body, CLI args). The Controller produces it; the Interactor consumes it.
- **Input Boundary:** decouples the Controller from the Interactor's identity. The Controller depends on the interface, not on the concrete Interactor.
- **Interactor:** the place where the application-specific logic lives. One operation, one Interactor.
- **Output Data:** decouples the result shape from the format (JSON, HTML, View Model). The Interactor produces it; the Presenter consumes it.
- **Output Boundary:** decouples the Interactor from the Presenter. The Interactor calls a Boundary; any Presenter can implement it (text, JSON, HTML, mobile).
- **Gateway interfaces:** decouple the Interactor from data and external services.

Without all six, parts of the system are coupled across circles and The Dependency Rule cannot be preserved.

## Example

For "create an invoice":

```
// Input Data (Use Cases circle)
class CreateInvoiceInputData {
  customerId: string;
  amount: Money;
  dueDate: Date;
}

// Input Boundary (Use Cases circle)
interface CreateInvoiceInputBoundary {
  execute(input: CreateInvoiceInputData): void;
}

// Output Data (Use Cases circle)
class CreateInvoiceOutputData {
  invoiceId: string;
  invoiceNumber: string;
  issuedAt: Date;
}

// Output Boundary (Use Cases circle)
interface CreateInvoiceOutputBoundary {
  present(output: CreateInvoiceOutputData): void;
  presentNotFound(customerId: string): void;
  presentValidationFailed(reason: string): void;
}

// Gateway interface (Use Cases circle)
interface InvoiceGateway {
  save(invoice: Invoice): Promise<void>;
  nextInvoiceNumber(): Promise<string>;
}

// Use Case Interactor (Use Cases circle)
class CreateInvoiceInteractor implements CreateInvoiceInputBoundary {
  constructor(
    private gateway: InvoiceGateway,
    private presenter: CreateInvoiceOutputBoundary,
  ) {}

  async execute(input: CreateInvoiceInputData): Promise<void> {
    // Application-specific business rules.
    // Calls Entity methods on Invoice.
    // Calls gateway to persist.
    // Calls presenter to deliver result.
  }
}
```

The Controller (Interface Adapters circle) builds the `CreateInvoiceInputData` from an HTTP request and calls the Interactor through the Input Boundary. The Presenter (Interface Adapters circle) implements the Output Boundary and builds a View Model that the View renders.

## Multiple methods on the Output Boundary

The Output Boundary often has more than one method, one per outcome:

- `present(output)` for success.
- `presentNotFound(...)` for missing resources.
- `presentValidationFailed(reason)` for invalid input.

This shape lets the Presenter handle each case differently (different HTTP status, different View Model). The Interactor calls the appropriate method; the Presenter knows nothing about HTTP status codes.

This contrasts with returning a result and letting the Controller branch on it. Clean's prescription is the Output Boundary call style; the alternative is acceptable in simpler systems but loses the ability to substitute the Presenter at runtime.

## When to collapse the structure

In smaller systems, some artefacts may be merged:

- **Output Boundary collapsed into a return value.** The Interactor returns Output Data; the Controller calls a Presenter that builds the View Model. Acceptable trade-off for simplicity. Cost: the Presenter cannot be substituted at runtime; the Interactor knows it returns a value rather than calling a Boundary.
- **Input Data merged with Input Boundary parameters.** Smaller systems pass parameters directly to a method. Acceptable. Cost: the call site is more coupled to the Interactor's signature.
- **Multiple use cases in one Interactor.** Strongly discouraged. Each use case is one Interactor; collapsing them produces god classes.

Document the deliberate collapses. Drift into "we did not implement Boundaries because we forgot" is not the same as "we deliberately collapsed for this reason".

## Anti-patterns specific to clean's Use Case structure

- **Interactor returns Output Data instead of calling Output Boundary, in a system that benefits from substitution.** A test cannot inject a fake Presenter; one HTTP response shape is hardcoded.
- **Controller passes the HTTP request directly to the Interactor.** Input Data missing; coupling reaches into the Interactor.
- **Presenter is the Controller.** Two named roles collapsed into one. View Model construction mixed with HTTP handling.
- **Gateway interface in Interface Adapters circle.** The interface is the consumer's contract; it lives with the consumer (the Use Case). Putting it next to its implementation inverts the direction.
- **One Interactor per HTTP endpoint.** Endpoints and use cases are not the same. One use case may have multiple entry points; one entry point may dispatch to one use case.

## Output

For each use case in the codebase, the team can answer:

- What is the Input Data? Output Data?
- Where is the Input Boundary defined and implemented?
- Where is the Output Boundary defined and implemented?
- Which Gateway interfaces does the Interactor depend on?
- Are deliberate collapses documented?

Without these answers, "we have use cases" is a label, not a structure.
