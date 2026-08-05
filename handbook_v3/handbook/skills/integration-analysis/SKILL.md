# Integration Analysis Skill

## When to use

The user asked about integrations between services, contexts, or third-party systems: contracts, retries, idempotency, timeouts, failure across boundaries, sync vs async choice for one specific integration, anti-corruption layer placement.

## When not to use

- Public-facing API design: `api-design`.
- Event-driven communication as the system's main paradigm: `event-driven-architecture`.
- Bounded context map and DDD-level relationships: `ddd` (context map).
- Microservice deployment topology decisions: `microservices-architecture`.

## Inputs to inspect first

- The two systems involved: who owns each, what each does.
- Existing contract: documented or implicit; version strategy.
- Communication style: sync (HTTP, gRPC) or async (events, queues).
- Failure modes: timeouts, retries, dead-letter, circuit breakers.
- Idempotency: at the producer, at the consumer, or both.
- Coupling: are internal models leaking, or is there an ACL?
- Operational posture: monitoring of the integration, alerts on failure.

## How to work

1. Identify the two systems and the direction.
2. Walk the contract: documented? versioned? owned?
3. Walk failure modes: timeouts, retries, idempotency.
4. Walk coupling: ACL or direct model leak.
5. Group findings by severity. Recommend minimal contract or behaviour changes.

## Output

Findings grouped: blockers (no idempotency on retried writes, no timeouts on synchronous calls, undocumented contract), defects (weak versioning, internal model leakage, missing DLQ on async), nits (naming consistency in the contract).

## Escalation

- The integration is many-to-many or systemic: `event-driven-architecture` or `microservices-architecture`.
- Public-facing API contract: `api-design`.
- Cross-context language and ACL design: `ddd`.
- Cost/security on the integration: `security-review`, `compliance-patterns`.

---

## Purpose

Evaluate integrations between modules, services, repositories, or external systems.

## Responsibilities

- Identify integration boundaries
- Detect contracts, APIs, events, schemas, and shared types involved
- Evaluate coupling between producer and consumer
- Identify compatibility and versioning risks
- Detect data consistency and failure-mode risks
- Recommend integration direction when useful

## Instructions

- Start by identifying the systems involved
- Identify the contract between them
- Check whether the contract is explicit and owned
- Check whether changes are backward compatible
- Identify sync vs async communication implications
- Identify retry, idempotency, ordering, and failure concerns when relevant
- Consider whether the integration leaks internal models across boundaries

## Heuristics

Treat as higher-risk integration when:

- Contracts are implicit or undocumented
- Internal domain models are exposed externally
- Events or APIs are consumed by multiple services
- Compatibility or versioning is unclear
- Failures can cause money, auth, reconciliation, or data consistency issues
- Ordering, retries, or idempotency are relevant but undefined

Treat as healthier integration when:

- Contracts are explicit and stable
- Ownership is clear
- Consumers are known
- Backward compatibility is preserved
- Failure modes are handled deliberately
- Internal models are translated at boundaries

## Rules

- Make contracts explicit
- Do not leak internal models across service boundaries
- Consider backward compatibility before changing contracts
- Make consistency and failure expectations explicit
- Prefer simple integration when coupling is acceptable
- Prefer events only when decoupling or asynchronous behavior adds value

## Activity Traceability

🔧 Loading skill: `integration-analysis`