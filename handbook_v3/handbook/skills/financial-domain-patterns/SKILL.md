# Financial Domain Patterns Skill

## When to use

The user asked about modelling money, currencies, taxes, accounting plan, invoicing, ledger entries, payment flows, financial dates and periods. Domain-specific.

## When not to use

- Generic domain modelling without financial specifics: `ddd`.
- Compliance and audit trails: `compliance-patterns`.
- Database schema in general: `database-design`.

## Inputs to inspect first

- Currency model: monetary value object, decimal precision, currency conversion strategy.
- Tax model: rates, periods, jurisdictions.
- Accounting plan: chart of accounts; double-entry vs single-entry; period closing.
- Invoice model: numbering, immutability, corrections (rectifying invoices).
- Reconciliation flows.

## How to work

1. Inspect the domain model in financial terms.
2. Walk concerns specific to money (precision, rounding, currency mismatches, date semantics).
3. Group findings.

## Output

Findings tied to the financial concept they violate.

## Escalation

- Audit/retention: `compliance-patterns`.
- Database shape for financial records: `database-design`.
- Domain modelling at large: `ddd`.

---

## Purpose

Evaluate whether financial domain code correctly models monetary concepts, enforces accounting invariants, and protects data integrity specific to fintech applications.

## Responsibilities

- Detect money represented as raw numbers or floats instead of dedicated value objects
- Identify missing or weak monetary precision handling
- Check for double-entry accounting invariants in accounting operations
- Detect mutable state on closed or settled financial records
- Identify missing idempotency in financial operations arriving from external systems
- Check that fiscal identifiers and accounting codes are typed as value objects, not raw strings
- Evaluate whether external financial data is transformed through explicit builders

## Instructions

- Start from the domain entities and value objects
- Check that every monetary amount uses a Money VO with an explicit Currency, not a raw number
- Check that Currency determines decimal precision — callers do not hardcode decimal places
- Look for TaxRate, TaxCode, and AccountingAccountId represented as primitives
- Check that use cases creating financial records from external sources include an idempotency key or external reference
- Look for direct mutations on aggregates in terminal states (SUCCEEDED, FAILED, CLOSED, SETTLED)
- When data from external systems is transformed into domain entities, check that a builder class owns that transformation
- Evaluate whether the double-entry invariant (debits = credits) is enforced at the boundary, not left to the caller

## Heuristics

Treat as stronger concerns when:

- `amount: number` or `price: number` appears on a domain entity or use case input
- A tax rate is a plain number without a corresponding VO
- An accounting code or fiscal identifier is validated inline in a handler or controller
- A use case that creates financial records from an external source has no idempotency key or external reference
- An aggregate in a terminal state (SUCCEEDED, FAILED, CLOSED, SETTLED) is mutated directly
- An external payload is mapped to a domain entity inside a use case or controller rather than via a dedicated builder
- The same currency string is parsed and validated in multiple places

Treat as acceptable when:

- A DTO or infrastructure model uses raw numbers — only the domain entity requires the VO
- A builder internally uses raw numbers before constructing the VO at the boundary
- A read-only query model or projection does not apply domain VOs

## Rules

- A `Money` type without an associated `Currency` is incomplete — always require both
- Immutability of closed records is non-negotiable: model corrections as new records, not updates to existing ones
- Idempotency is required for any operation triggered by an external event, message, or API call
- Double-entry enforcement belongs at the builder or aggregate boundary, not in application orchestration
- Do not apply these rules to infrastructure models, read models, or DTOs — only to domain entities, aggregates, and the code that enforces domain invariants

## Activity Traceability

🔧 Loading skill: `financial-domain-patterns`
