# Integration Analysis

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