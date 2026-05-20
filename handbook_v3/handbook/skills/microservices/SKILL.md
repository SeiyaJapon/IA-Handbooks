# Microservices

## Purpose

Review microservices design decisions, inter-service communication patterns, and operational characteristics for correctness, resilience, and maintainability.

## Responsibilities

- Evaluate service boundary and ownership design
- Assess inter-service communication: synchronous vs asynchronous
- Review data ownership and cross-service data access patterns
- Evaluate failure isolation and resilience patterns (circuit breaker, retry, timeout)
- Assess service contract design and backwards compatibility
- Review distributed transaction patterns
- Evaluate service discovery and configuration management
- Detect tight coupling between services

## Instructions

- Identify the services involved and their ownership boundaries
- Check communication model: synchronous HTTP (request/response) or async messaging?
- Check data ownership: does each service own its data? are there shared databases?
- Check failure handling: is there a circuit breaker? are retries bounded with backoff?
- Check contracts: are breaking changes versioned? do consumers have backward compatibility?
- Check distributed transactions: are they handled with sagas or compensating transactions?
- Check deployment independence: can each service be deployed without coordinating with others?
- Ask: if this service is down, what breaks? is that acceptable?

## Heuristics

Treat as stronger concerns when:

- Shared database between multiple services (tight data coupling)
- Synchronous chain of calls across three or more services (cascading failure risk)
- No circuit breaker or timeout on inter-service calls
- Service A importing types or models from service B's codebase
- Distributed transactions using 2PC (two-phase commit) instead of sagas
- Breaking API changes without versioning or deprecation
- Services deployed together instead of independently
- Configuration hard-coded per service instead of centralized or environment-driven

Treat as acceptable when:

- Synchronous calls between two tightly related services in a single domain
- Shared library of common types with a well-defined, stable contract

## Rules

- Services must own their data — no shared databases
- Inter-service calls must have timeouts and failure handling
- Breaking contract changes require versioning
- Deployment independence is a design constraint, not an aspiration

## Activity Traceability

🔧 Loading skill: `microservices`
