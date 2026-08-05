# 05. The External Layer

The outermost ring of onion. Holds **everything outside the domain**: persistence, transport, framework, UI, external services.

This is onion's vocabulary: **External Layer** (sometimes also called Infrastructure Layer or Infrastructure ring). Hexagonal calls this region "infrastructure"; clean splits it into "Interface Adapters" and "Frameworks & Drivers". Onion keeps it as one outer ring.

## What lives in the External Layer

- **Repository implementations.** Concrete classes that implement the repository interfaces declared by Application Services.
- **External service clients.** Concrete classes that call third-party APIs.
- **Event bus implementations.** Concrete classes that publish/consume events.
- **HTTP controllers, CLI handlers, queue consumers.** Driving entries to the inner rings.
- **UI code.** When onion is used in desktop or rich-client applications.
- **Framework wiring.** DI configuration, application bootstrap, framework lifecycle hooks.
- **Database drivers.** The Postgres client, the MongoDB driver, the broker library.
- **Logging and observability infrastructure.** When implementing interfaces declared by inner rings.

## What does NOT live here

- Business rules. Those are in Domain Model or Domain Services.
- Use case orchestration. That is Application Services.
- Interfaces declared by inner rings. Those live in the ring that needs them, not in External.

## How External Layer satisfies inner rings

Inner rings declare interfaces; External Layer implements them.

```
// Application Services ring
interface InvoiceRepository {
  save(invoice: Invoice): Promise<void>;
  findById(id: InvoiceId): Promise<Invoice | null>;
}

// External Layer
class PostgresInvoiceRepository implements InvoiceRepository {
  constructor(private db: PostgresClient) {}

  async save(invoice: Invoice): Promise<void> {
    // SQL operations.
  }

  async findById(id: InvoiceId): Promise<Invoice | null> {
    // SQL query, mapping to domain.
  }
}
```

The Application Service depends on `InvoiceRepository` (the interface, in its own ring). The Postgres implementation lives in External Layer. The composition root wires the implementation to the consumer.

## What onion does NOT prescribe in the External Layer

Onion does not split the External Layer into named adapter roles. Specifically:

- **Onion does not have "driving / driven adapters".** That is hexagonal vocabulary.
- **Onion does not have "Controller / Presenter / Gateway" as separate prescribed roles.** That is clean vocabulary.

In an onion codebase, the External Layer can contain:

- HTTP controllers (entry points).
- Repository implementations.
- External clients.
- Event bus implementations.
- Framework modules.

The team may organise them by sub-folder for clarity (e.g. `external/persistence/`, `external/web/`, `external/clients/`), but the architectural prescription is just "External Layer". The further organisation is convention.

## Replaceability

Because dependencies point inward and the External Layer is at the edge:

- Replacing the database requires rewriting the repository implementations.
- Replacing the framework requires rewriting the wiring layer and possibly the controllers.
- It does not require rewriting Application Services, Domain Services, or Domain Model.

The benefit of onion (and hexagonal and clean): stable code stays untouched when external choices change.

## Onion vs clean and hexagonal in the External Layer

| Aspect | Onion | Hexagonal | Clean |
|---|---|---|---|
| Outermost name | External Layer | Infrastructure | Frameworks & Drivers + Interface Adapters (split into two) |
| Adapter roles named | No | Yes (driving / driven) | Yes (Controller / Presenter / Gateway) |
| Splits roles into separate sub-rings | No | No | Yes (Interface Adapters vs Frameworks & Drivers) |

Onion is the simplest of the three at the outer ring: one layer, no prescribed sub-roles.

## Anti-patterns

### External Layer with business rules

A repository or controller decides "if status is X, do Y" with business meaning.

Fix: business decisions belong in Domain Model, Domain Services, or Application Services.

### Inner ring imports External Layer

Domain Model imports a concrete repository. Application Services imports a concrete external client.

Fix: invert through interfaces. The interface lives in the consuming ring; External implements.

### External Layer leaking technology types into inner rings

A repository returns ORM rows directly to the Application Service.

Fix: the repository translates between technology types and domain types. The Application Service sees only domain types.

### External as a god ring

Everything that is not domain piles into one massive External Layer with no sub-organisation.

Fix: organise by capability (`external/persistence/`, `external/web/`, `external/messaging/`) for clarity. Convention, not architectural prescription.

## Output

For the External Layer, the team can answer:

- What lives here?
- Which interfaces (declared by inner rings) does each piece implement?
- Are there business rules leaking in?
- Are technology types contained, or leaking inward?
- Is the layer organised by capability sub-folder, or is it a god ring?
