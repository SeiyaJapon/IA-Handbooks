# Compliance Patterns Skill

## When to use

The user asked about regulated data, audit trail, data retention, non-repudiation, gap-free invoice numbering, payment-data handling, financial compliance, or other regulatory concerns.

## When not to use

- Generic security: `security-review`.
- API contract auth design: `api-design` + `security-review`.
- Operational logging without compliance constraint: `observability`.

## Inputs to inspect first

- Data classification: regulated record vs operational data.
- Persistence policy: hard delete allowed? soft delete only?
- Audit trail: actor, change, timestamp captured per critical operation.
- Retention rules.
- Sequence-controlled identifiers (invoice numbers, ledger sequences).
- User-deletion flows that could touch regulated records.

## How to work

1. Identify regulated records.
2. Walk concerns: traceability, immutability, retention, actor identity, sensitive data scope.
3. Group findings by severity.

## Output

Findings grouped by regulatory concern, citing the affected entity and operation.

## Escalation

- Generic security: `security-review`.
- Database schema for audit: `database-design`.
- Financial domain specifics: `financial-domain-patterns`.

---

## Purpose

Evaluate whether software handling regulated financial data meets traceability, immutability, data retention, and privacy requirements specific to fintech and e-invoicing contexts.

## Responsibilities

- Distinguish application logging from legal audit trails
- Detect mutable or hard-deletable financial records that should be immutable
- Identify flows where user data deletion could reach regulated financial records
- Check that critical operations capture a verifiable actor identity
- Detect overly broad persistence of sensitive payment data
- Evaluate whether invoice numbering sequences preserve legal gap-free requirements

## Instructions

- Identify the data classification: regulated financial record (invoice, transaction, ledger entry) or operational data
- Check whether state changes on regulated records are traceable in the data model: who made the change, what changed, when
- Check whether the persistence layer allows hard-deletes on regulated records
- Evaluate whether user deletion workflows are scoped to exclude regulated records
- Check whether use cases that issue or settle financial records capture an authenticated actor
- Look for full card numbers, IBANs, or other sensitive payment data stored on business entities
- Check that invoice number generation is delegated to a sequence-controlling VO or service that enforces continuity

## Heuristics

Treat as stronger concerns when:

- State changes on invoices, transactions, or ledger entries are only visible in application logs, not in the data model
- A repository or service exposes a hard-delete method on regulated financial records
- A user account deletion flow reaches invoice or transaction tables without exclusion logic
- A use case modifying financial records does not require or capture an authenticated user context
- A domain entity or infrastructure model stores a full card number, CVV, or full IBAN
- Invoice number generation happens outside a sequence-controlling VO or service

Treat as acceptable when:

- Operational records unrelated to financial obligations (drafts, internal notes, preview data) can be hard-deleted
- Anonymization replaces personal data while leaving the financial record structure intact
- Soft-delete is used with a retention policy that prevents physical deletion before the retention period expires

## Rules

- Application logs are not a substitute for a legal audit trail — they can be truncated, rotated, or lost
- Spanish tax law (Ley General Tributaria) requires a minimum 4-year retention period for fiscal records; treat 5 years as the safe minimum
- Non-repudiation requires capturing an actor identity at write time — it cannot be reconstructed after the fact
- PCI-DSS scope minimization: do not store what is not needed; truncate or tokenize payment data
- Immutability of regulated financial records is non-negotiable; flag any deviation as high risk

## Activity Traceability

🔧 Loading skill: `compliance-patterns`
