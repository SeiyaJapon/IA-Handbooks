# Testing Strategy Skill

## When to use

The user asked what to test, at what level, with what tooling, or how to set up a test suite. Includes test pyramid decisions, mocking strategy, contract tests, integration tests, end-to-end tests, eval-as-test for AI systems.

## When not to use

- Reviewing the idiomatic correctness of test code (lint, naming, fixtures): use the corresponding language review skill.
- Verifying a single test passes: outside this skill (operational).
- AI-specific evaluation (golden datasets, LLM-as-judge): `llm-ops` or `ml-review`.

## Inputs to inspect first

- Test framework in use; test runner config.
- Test layout: unit/integration/e2e separation.
- Mocking strategy and where boundaries are mocked.
- CI integration: when tests run, how flaky tests are handled.
- Coverage signals (when meaningful).

## How to work

1. Identify what is currently tested and at what level.
2. Identify the missing layers (unit, integration, contract, e2e).
3. Walk test-design concerns: fast vs slow tests, isolation, fixtures, deterministic vs flaky.
4. Recommend the smallest change in test strategy that closes the active gap.

## Output

Findings grouped: blockers (no tests on critical paths, untested error paths, flaky tests merged), defects (over-mocking, slow unit tests, missing integration), nits (organisation, fixture sharing).

## Escalation

- Idiomatic test code in a specific language: language review skill.
- AI evaluation specifically: `llm-ops`, `ml-review`.
- Performance benchmarks: `performance-analysis`.
- Architecture-level test boundaries: `software-architecture`.

---

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