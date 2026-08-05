# 03. The Dependency Rule

The defining property of onion: **source code dependencies point inward only**.

Outer rings know about inner rings; inner rings do not know about outer rings.

```
External Layer
    |  (depends on)
    v
Application Services
    |  (depends on)
    v
Domain Services
    |  (depends on)
    v
Domain Model
```

## Statement

- Domain Model depends on **nothing**.
- Domain Services depend on **Domain Model only**.
- Application Services depend on **Domain Services and Domain Model only**.
- External Layer depends on **any inner ring** (Application Services, Domain Services, Domain Model).

The compile-time direction of imports is inward. Runtime calls can flow outward (an Application Service calling a repository implementation), but the source-code dependency stays inward through interfaces.

## Inversion through interfaces

When an inner ring needs something from an outer ring, the inner ring **declares an interface** in its own vocabulary. The outer ring implements the interface.

In onion's vocabulary:

- The interface is **owned by the ring that needs it** (typically Application Services).
- The interface is implemented in the **External Layer**.
- Onion does not call these interfaces "ports" (that is hexagonal) or "Boundaries" (that is clean). They are simply interfaces, owned by the consuming ring.

Example:

```
// Application Services ring
interface InvoiceRepository {
  save(invoice: Invoice): Promise<void>;
  findById(id: InvoiceId): Promise<Invoice | null>;
}

// External Layer
class PostgresInvoiceRepository implements InvoiceRepository {
  // implementation using Postgres driver
}
```

The Application Service depends on `InvoiceRepository` (the interface). The Postgres implementation lives in External Layer. The composition root wires the implementation to the consumer.

## Forbidden imports

- Domain Model imports from Domain Services, Application Services, or External: forbidden.
- Domain Services imports from Application Services or External: forbidden.
- Application Services imports a concrete External implementation: forbidden (must import the interface, not the concrete).
- Any framework, ORM, HTTP, or SDK import inside Domain Model, Domain Services, or Application Services: forbidden.

## Examples

### Allowed

- `domain-services/RiskScoreEvaluator.ts` imports `domain/Scenario.ts`. Domain Services → Domain Model. ✓
- `application/EvaluateRiskUseCase.ts` imports `domain-services/RiskScoreEvaluator.ts`. Application Services → Domain Services. ✓
- `application/EvaluateRiskUseCase.ts` imports `application/RiskRepository.ts` (the interface). Within Application Services. ✓
- `infrastructure/PostgresRiskRepository.ts` imports `application/RiskRepository.ts`. External → Application Services. ✓
- `main.ts` wires concrete repositories to use cases. External → inner rings. ✓

### Forbidden

- `domain/Scenario.ts` imports `infrastructure/...`. Domain Model → External. ✗
- `application/EvaluateRiskUseCase.ts` imports `infrastructure/PostgresRiskRepository.ts`. Application Services → concrete External. ✗
- `domain/Scenario.ts` has `@Entity` annotation from an ORM. Domain Model coupled to External. ✗
- `domain-services/RiskScoreEvaluator.ts` reaches the database directly. Domain Services coupled to External. ✗

## Enforcement

The dependency rule is enforced through:

- **Lint rules** that forbid imports across the wrong direction.
- **Dependency graph tools** that visualise the import graph.
- **Architecture tests** that fail when imports cross the rule.
- **Code review** for what tooling cannot catch.
- **No exceptions for "just this one time".**

Without enforcement, "we are onion" is a label, not a property.

## Why the inward direction

The benefit is **isolation of stable code from volatile code**:

- Domain Model is the most stable. It survives framework, ORM, transport changes.
- Domain Services change when business rules change.
- Application Services change when use cases change.
- External Layer is the most volatile. It absorbs vendor changes, infrastructure migrations, framework upgrades.

The rule keeps the stable rings independent of the volatile rings. Replacing the database does not affect Domain Model. Replacing the framework does not affect Domain Services or Application Services.

## Comparison

This is the **same dependency rule** as hexagonal and clean. The difference is the **granularity of the rings**, not the rule. All three architectures fail in the same ways when the rule is not enforced.

## Output

For the dependency rule to be sound, the team can answer:

- For every import in the codebase, is the direction valid?
- Are interfaces owned by the inner ring that needs them, not by the outer ring that implements them?
- Is the rule enforced (lint, dep-graph, review)?
- What pragmatic exceptions, if any, are documented?

Without enforcement, the rule decays. Onion in name only, layered in practice.
