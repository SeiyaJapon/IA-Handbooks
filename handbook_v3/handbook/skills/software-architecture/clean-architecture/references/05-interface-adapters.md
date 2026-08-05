# 05. Interface Adapters

The third circle from the centre. Adapts between the inner circles' format and outer formats. Clean splits this circle into **three named roles**: Controller, Presenter, Gateway.

The three roles are distinct in clean. Hexagonal calls them all "adapters"; clean separates them deliberately.

## Controller

Driving entry. Receives input from outside (HTTP request, CLI arguments, queue message), translates it into **Input Data**, calls the Use Case through the **Input Boundary**.

Properties:

- Knows the transport (HTTP, CLI, queue).
- Translates input from the transport's vocabulary to the Use Case's Input Data.
- Calls the Input Boundary.
- Returns or hands off control. The Controller does **not** format the response in clean (the Presenter does).

Examples: `CreateInvoiceController` (HTTP), `ListProductsCliCommand` (CLI), `OrderPlacedConsumer` (queue).

What a Controller does NOT do:

- Build response shapes (JSON, HTML). That is the Presenter.
- Contain business rules. That is the Interactor or the Entity.
- Talk to the database. That is the Gateway.

## Presenter

Output side. Implements the **Output Boundary** declared by the Use Case. The Interactor calls the Presenter (through the Output Boundary) to deliver the result. The Presenter builds the **View Model** consumed by the View.

Properties:

- Knows the output format (JSON shape, HTML structure, View Model fields).
- Receives **Output Data** from the Interactor.
- Builds a **View Model** in the format the View expects.
- Has multiple methods (one per outcome): `present(output)`, `presentNotFound(...)`, `presentValidationFailed(reason)`.

Why Presenter is separate from Controller:

- A single Use Case may be presented in multiple formats (JSON for API, HTML for web, plain text for CLI). Each Presenter is a different class.
- The Presenter is the only place that knows the output format. The Interactor returns Output Data shaped by the domain, not by the format.
- Tests can substitute the Presenter (a test Presenter that captures the call for assertions) without involving HTTP machinery.

Examples: `CreateInvoiceJsonPresenter`, `CreateInvoiceHtmlPresenter`, `OrderConfirmationEmailPresenter`.

What a Presenter does NOT do:

- Decide what the operation does. That is the Interactor.
- Talk to the database. That is the Gateway.
- Handle the HTTP response directly (in some setups, the Controller picks up the View Model the Presenter produced and writes the HTTP response; in others, the Presenter writes directly. Either way, the Presenter's job is the View Model).

## Gateway

Driven side. Implements the **Gateway interface** declared by the Use Cases. Talks to the database, external API, file system.

Properties:

- One Gateway per Gateway interface. Each Gateway is a one-to-one mapping.
- Translates between the Use Case's domain types and the technology's vocabulary (rows, JSON, SDK calls).
- Handles technology-specific concerns: connection pooling, retries, error translation.
- Owns no business rules.

Examples: `PostgresInvoiceGateway`, `StripePaymentGateway`, `S3DocumentGateway`.

The term **Gateway** is clean's specific vocabulary. Hexagonal calls these "driven adapters" or specifically "repositories" / "external clients". Onion does not separate them by name. Clean uses Gateway as the umbrella term for any adapter that satisfies a Gateway interface declared by a Use Case.

What a Gateway does NOT do:

- Contain business rules.
- Coordinate use cases.
- Call other Gateways. Composition belongs in the Use Case.

## The three roles, side by side

```
[outside world]
      |
      v
  [Controller] ------builds Input Data------> [Input Boundary]  ←implemented by Interactor
                                                                       |
                                                                       | calls
                                                                       v
                                                              [Gateway interface]
                                                                       ^
                                                                       | implements
                                                              [Gateway concrete]
                                                                       |
                                                                       v
                                                                 [database]

  [Interactor] ------calls Output Boundary------> [Presenter]
                                                       |
                                                       | builds View Model
                                                       v
                                                  [outside world]
```

## Where Interface Adapters live

Typically:

```
infrastructure/
├── web/                  Controllers, Presenters for HTTP.
├── cli/                  Controllers (and possibly Presenters) for CLI.
├── messaging/            Consumers (a kind of Controller) for queues.
├── persistence/          Gateways for databases.
├── clients/              Gateways for external services.
└── ...
```

The folder structure is convention. The architecture is the role each class plays and the dependency direction.

## Anti-patterns specific to Interface Adapters in clean

- **Controller that builds the response.** Should hand off to a Presenter through the Output Boundary.
- **Gateway interface in the Interface Adapters circle.** The interface lives with its consumer (the Use Case).
- **Generic Gateway.** `Gateway<T>` shaped by persistence, not by the domain.
- **Gateway with business rules.** "If the row's status is X, decide Y." The decision belongs in the Interactor or Entity.
- **Presenter that calls a Gateway.** Presenters format. Gateway calls are Interactor's job.
- **Controller calling a Gateway directly.** Bypasses the Use Case. The architecture's value (Use Case as the single source of operation logic) is lost.
- **Three roles collapsed into "service".** Loses the clean-specific clarity.

## Output

For each piece in the Interface Adapters circle, the team can answer:

- Which role (Controller, Presenter, Gateway)?
- Which Boundary (Input, Output) or Gateway interface does it relate to?
- Does it translate without leaking outward types into inner circles?
- Is it free of business rules?

Without these, the circle becomes a generic "adapters" dumping ground, which is hexagonal vocabulary, not clean's.
