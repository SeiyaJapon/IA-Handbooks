# Testing Strategy

## Purpose

Define how to validate that the system behaves correctly with the appropriate level of testing.

## Responsibilities

- Identify what needs to be tested and why
- Determine the appropriate level of testing (unit, integration, contract, e2e)
- Ensure business behavior is covered by tests
- Detect gaps in testability
- Evaluate whether the design enables or hinders testing
- Recommend a testing approach when useful

## Instructions

- Start from the behavior that must be guaranteed
- Identify critical paths, invariants, and business rules
- Prefer testing business behavior over implementation details
- Identify boundaries where integration tests are needed
- Identify contracts that require validation (APIs, events, schemas)
- Ensure tests are aligned with architectural boundaries
- Avoid over-testing trivial or low-value logic

## Heuristics

Prefer unit tests when:

- Testing domain logic, invariants, and pure functions
- Behavior is isolated and deterministic
- Dependencies can be replaced by simple doubles

Prefer integration tests when:

- Testing interaction between components or layers
- Verifying persistence, messaging, or external integrations
- Validating adapters or infrastructure behavior

Prefer contract tests when:

- Validating APIs, events, or schemas shared across services
- Ensuring compatibility between producers and consumers

Prefer end-to-end tests when:

- Validating critical user flows
- Ensuring system-wide behavior works as expected

Treat as testing concerns when:

- Business logic is hard to test due to coupling
- Tests depend heavily on infrastructure
- Setup is complex or fragile
- Behavior is not clearly verifiable

## Rules

- Test behavior, not implementation details
- Keep tests aligned with architectural boundaries
- Prefer simple, reliable tests over complex setups
- Do not over-test trivial code
- Ensure critical business rules are covered
- Explain testing trade-offs when relevant

## Activity Traceability

🔧 Loading skill: `testing-strategy`