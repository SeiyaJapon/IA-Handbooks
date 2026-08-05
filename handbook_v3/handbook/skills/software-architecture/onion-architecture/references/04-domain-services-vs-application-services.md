# 04. Domain Services vs Application Services

This is **onion's distinguishing axis**. The explicit separation of these two as distinct rings is what makes onion onion. Hexagonal collapses both into "the application core". Clean folds Domain Services into Entities or Use Cases. Onion keeps them separate.

This document defines each, the differences, and when each is the right home for a piece of behaviour.

## Domain Services ring

Behaviour that is **part of the domain** but does not belong to any single entity or value object. Cross-entity logic, intrinsic to the business.

### Properties

- Stateless or holds only domain-meaningful state.
- Named after the operation in domain terms (`RiskScoreEvaluator`, `EligibilityChecker`, `PriceCalculator`, `InvoiceNumberGenerator`).
- Speaks the ubiquitous language.
- No knowledge of transactions, frameworks, persistence, transports.
- Coordinates multiple Domain Model objects.
- Result is a domain-meaningful value or a state change in the entities passed in.

### Examples

- `RiskScoreEvaluator.evaluate(scenario, customer, history)`: combines multiple aggregates to produce a risk score. The risk score is a domain value, not a use case outcome.
- `EligibilityChecker.isEligible(customer, product)`: a domain rule that depends on two entities.
- `InvoiceNumberGenerator.next()`: enforces enterprise numbering rules; the rule is the business's, not the application's.
- `LimitCalculator.calculate(customer, contracts)`: derives a credit limit from multiple sources following business rules.

### When to use

- The behaviour requires multiple Domain Model objects.
- The behaviour is intrinsic to the domain : the business would describe it as "something the system does", regardless of how it is invoked.
- The behaviour would be reused by multiple use cases, each describing a different application context for the same intrinsic domain operation.

### When NOT to use

- The behaviour belongs to a single entity. Move it inside the entity in Domain Model.
- The behaviour is orchestration of a use case. It belongs in Application Services.
- The behaviour is generic / utility (logging, formatting, technical helpers). Move to External Layer or a utility module.

## Application Services ring

**Use cases**. Orchestration of Domain Model and Domain Services to satisfy application operations.

### Properties

- One Application Service per business operation (`CreateScenarioUseCase`, `ApprovePaymentUseCase`).
- Thin orchestrator: load aggregates, call domain methods (entities or Domain Services), save, dispatch events.
- Knows about transactions and orchestration.
- Declares interfaces for what it needs from External Layer (Repository, Notification, Clock, EventBus).
- No framework, no transport.

### Example

- `EvaluateRiskUseCase.execute(command)`: loads the scenario via `ScenarioRepository` (interface), calls `RiskScoreEvaluator` (Domain Service), saves the result, dispatches a `RiskEvaluated` event through `EventBus` (interface).

The use case orchestrates. The risk evaluation logic itself is in the Domain Service.

### When to use

- The behaviour is one specific operation the application offers.
- The behaviour orchestrates multiple domain calls (Domain Model + Domain Services) into a single use case.
- The behaviour involves transactions, persistence, event dispatch.

### When NOT to use

- The behaviour is intrinsic domain logic with no orchestration aspect. It belongs in Domain Services.
- The behaviour belongs to a single entity. It belongs in Domain Model (an entity method).

## The line

The clearest test: **could this be reused by a different application of the same business?**

- **Yes**: it is a Domain Service. The rule belongs to the business; it would be in any application built around the same domain.
- **No**: it is an Application Service. The orchestration is specific to this application's use case.

A second test: **does the behaviour describe what the business does, or what this application does?**

- "What the business does": Domain Service.
- "What this application does": Application Service.

## Examples worked through

### "Compute the discount of an invoice"

- If the discount rule is intrinsic to the business ("loyal customers get 10%"), it is a Domain Service, or an entity method on `Customer` if it depends only on the customer.
- If the discount calculation depends on application-specific policies (a current promotion period configured in this app, discount stacking rules specific to this app), it may live in Application Services.
- If the discount is a property of the invoice entity itself ("this invoice has a 10% discount applied"), it is on the entity in Domain Model.

### "Send an invoice to a customer"

- The act of sending (calling email service, PDF service, persistence) is orchestration: Application Services.
- The decision of when an invoice can be sent (state machine, business rules) is domain: Domain Services or entity method.

### "Calculate VAT"

- The rate lookup and arithmetic are intrinsic to the business: Domain Services or value objects.
- The use case "issue an invoice with VAT" is Application Services.

### "Generate a unique invoice number"

- The rule for generating valid invoice numbers (format, sequence, gap-free, country-specific) is intrinsic: Domain Service `InvoiceNumberGenerator` (possibly stateful for the sequence).
- The act of issuing an invoice with a number is the use case in Application Services.

## When the distinction collapses

In small systems, Domain Services may be empty (no cross-entity behaviour). In that case, onion is heavier than necessary; hexagonal or clean is a lighter fit.

Conversely, in very rich domains, Domain Services may be many and complex. Onion's explicit ring shines in those cases by giving the team a clear place to put cross-entity domain logic.

If the team finds Domain Services collapsing into Application Services repeatedly, it is a sign that:

- Either the domain has no real cross-entity behaviour (move to a lighter architecture), or
- The team is conflating intrinsic domain logic with orchestration (re-train).

## Anti-patterns

### Domain Services as a dumping ground

Anything that does not fit in an entity dropped in Domain Services. The ring becomes a generic services folder.

Fix: classify. Domain logic that crosses entities → Domain Service. Orchestration of a use case → Application Service. Utility → utility module.

### Application Services with intrinsic domain rules

A use case contains business rules that should be in Domain Services or entities.

Fix: extract the rule into a Domain Service or move it into an entity in Domain Model.

### Empty Domain Services ring

The team adopted onion but the ring is empty.

Fix: evaluate hexagonal or clean. They collapse the two rings; if the domain genuinely has no cross-entity behaviour, those architectures fit better.

### Domain Service that calls infrastructure

A Domain Service queries the database or calls an external API.

Fix: pure domain logic. If data is needed, the Application Service loads it through interfaces and passes it to the Domain Service as input.

## Output

For each piece of behaviour the team writes, ask:

- Is it a property of one entity? → entity method in Domain Model.
- Does it cross multiple entities and is intrinsic to the business? → Domain Service.
- Does it orchestrate domain operations to satisfy a specific use case of this application? → Application Service.

Without this discipline, behaviour drifts: rules end up in use cases (anaemic domain), or use cases end up containing rules (fat use cases). Either way, onion's distinguishing benefit is lost.
