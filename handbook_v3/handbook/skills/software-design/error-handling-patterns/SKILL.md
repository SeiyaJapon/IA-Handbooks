# Error Handling Patterns Skill

Design and review error handling **at the function/module level**: error types, propagation, boundaries, recovery, and consistency. This skill stops at the boundary of one application's code; cross-boundary failures (services, queues, distributed systems, observability, incidents) escalate to other skills.

## When to use

Use this skill when:

- The user asks to review error handling in a function, class, or module.
- A code review surfaces empty catch blocks, generic exceptions, lost context, or inconsistent error responses.
- A team is deciding between exceptions and result types, or designing a domain error hierarchy.
- A function fails silently and the team is debugging propagation.
- A controller or HTTP layer is mapping internal errors to status codes inconsistently.
- A library or service is being designed and the public error contract is being defined.

## When not to use

Do not use this skill when:

- The failure is **across services** (timeouts, retries, dead-letter, circuit breakers between services). That is `integration-analysis` or `event-driven-architecture`.
- The failure is about **events arriving twice or out of order**. That is `event-driven-architecture` (delivery semantics, idempotency, DLQ).
- The failure is about **production diagnosis** (alerts, dashboards, traces, log shape). That is `observability`.
- The failure is about **incident response** (paging, on-call, runbooks, postmortems). That is operational and outside this skill.
- The "error" is actually **a domain rule** that should be modelled (a state transition not allowed, an invariant violated). That is `ddd` or domain modelling; this skill picks up after the domain rule has been named.
- The "error" is **API contract design** (status codes, error envelopes, problem-details). That is `api-design`; this skill informs the implementation, but the contract is the API design's call.

## Inputs to inspect first

Before recommending error-handling changes, inspect:

- **Existing error types.** Is there a domain error hierarchy? Are application errors distinguished from infrastructure errors?
- **Catch blocks across the codebase.** Empty catches, blanket catches, swallowed errors.
- **Top-level handlers.** Is there a place that catches what nothing else catches, with logging and a sane response?
- **Re-throw patterns.** When errors are caught and re-thrown, is the original context preserved (cause, stack, payload)?
- **Public error contracts.** What does the API/library promise to its callers? Is internal detail leaking?
- **Logging patterns.** Are errors logged once at the right layer, or many times across layers?
- **The language/runtime conventions.** Exceptions vs result types; checked vs unchecked; `panic` vs error returns. The choice depends on the language and the team's convention.

If any of these is unknown, error-handling judgements are guessing.

## Error categories

Distinguishing categories is the first move. Each category has different handling rules.

- **Validation errors.** Input does not satisfy structural or format rules. Caller's fault. Map to 400-class responses; do not retry.
- **Domain rule violations.** The operation is not allowed by the business rules (a transition not permitted, an invariant violated). Caller's fault but in business terms. Distinct error types; map to 409/422 typically.
- **Authorisation / authentication errors.** The caller is not allowed to perform the operation, or is not authenticated. Distinct types; map to 401/403; do not leak which case it is in some security contexts.
- **Concurrency conflicts.** Two operations race; one wins, the other must retry or surface a conflict. Distinct types; sometimes retryable by the client, sometimes not.
- **Idempotency conflicts.** The same operation is being performed again with a conflicting state. Distinct from validation; may need to return the prior result.
- **Infrastructure failures.** A dependency the application owns operationally (its database, its cache) is failing. Often retryable; alert on persistence; do not leak internals to clients.
- **Dependency failures.** An external system the application calls is failing. Retry with backoff; circuit-break; surface a "service unavailable" or fallback.
- **Programmer errors.** Assertions, null pointers, contract violations inside the code. Distinct from operational errors; these crash, log loudly, and trigger an incident.
- **Transient errors.** Network blips, temporary contention. Retry; do not alert on first occurrence; alert on repeated.
- **Partial failures.** A multi-step operation succeeded for some steps, failed for others. Compensation, sagas, or explicit partial-success responses.
- **Distributed-system failures.** Timeouts, lost messages, order anomalies. These belong to the distributed system's design, not to local error handling.

This skill covers everything up to "infrastructure failures". Anything cross-service or cross-process escalates.

## Hard rules

- **Silent failure is never acceptable.** Every catch logs, re-throws, or recovers explicitly. Empty catches are bugs.
- **Domain errors must be distinguishable from infrastructure errors.** A `NotFound` from the domain is different from a `DatabaseTimeout`. Different types, different handling, different responses.
- **Error context survives propagation.** When wrapping or re-throwing, preserve the cause. `throw new Error('failed')` that drops the original is data loss.
- **Internal detail does not leak across the public boundary.** Stack traces, query strings, ORM errors stay internal. Clients see a sanitised error.
- **One error, one log.** Logging the same error at three layers is noise. Log once, at the layer that has context.
- **Map the same error condition to the same response everywhere.** Inconsistency confuses callers and complicates retries.
- **Exceptions vs result types is a per-codebase choice, not a global rule.** Both can be designed well. Mixing both inconsistently is the actual mistake.

## When to use exceptions, result types, error objects

The choice is language-dependent and codebase-dependent. Generic guidance:

- **Exceptions** fit when failures are exceptional (truly), language-idiomatic (Java, Python, C#, JS at the application level), and the error path is much rarer than the happy path.
- **Result types** (`Result<T, E>`, `Either`, sum types) fit when the language supports them well (Rust, Go's `error`, TypeScript with discriminated unions, FP-heavy codebases) and when failures are routine outcomes that callers should explicitly handle.
- **Error objects** (returning `null` plus a side-channel error) fit poorly in most modern code; they tend to lose context.
- **Typed errors** (a hierarchy of error classes) help in either model: callers can pattern-match.

Avoid mixing models without intent. A codebase that throws in some functions, returns `Result` in others, and uses error callbacks in a third style is hard to reason about.

## Cross-cutting techniques

These belong here when applied **inside** the application; they belong elsewhere when crossing service boundaries.

- **Retries** at the function level for transient infrastructure failures. Bounded count, exponential backoff, jitter. Cross-service retries are integration's domain.
- **Circuit breakers** for dependency failures. Same caveat: cross-service circuit breakers are integration's.
- **Dead-letter handling** is queue/EDA territory. This skill covers what happens before the message reaches the queue, and what to do with errors local to the consumer.
- **Compensation** within one transaction's reach is local; sagas across services are EDA's.
- **Explicit failure states** (an `OrderState.Failed` rather than a thrown error) are a domain modelling choice; covered by `ddd` for the modelling decision.

## How to work

1. **Identify the boundary.** Local to this code? Cross-boundary? If cross-boundary, escalate to the right skill.
2. **Categorise the errors.** Validation, domain, auth, concurrency, idempotency, infra, dependency, programmer, transient, partial.
3. **Inspect the existing handling.** Empty catches, blanket catches, lost context, inconsistent mappings, multi-layer logging.
4. **Inspect the public contract.** What does the API or library promise? Is internal detail leaking?
5. **Inspect the test setup.** Errors are easier to model when tests exercise them; if no tests cover the error paths, the design is unverified.
6. **Recommend the smallest change.** Type a generic `Error` into a domain-meaningful error class. Add a top-level handler. Preserve cause on re-throw. Sanitise the public boundary. Remove duplicate logs.
7. **Refuse over-engineering.** Result types everywhere when the team and language idiom is exceptions; or vice versa. Pick one; apply consistently.

## Output

Return findings as:

- **Concrete violations:** silent failure, generic types, lost context, inconsistent mapping, leaked internals, duplicate logs.
- **Recommended changes:** minimal type changes; minimal placement changes; consistent mapping at the boundary.
- **Boundary escalations:** when the issue is cross-service, queue, observability, or incident.
- **Refusal to change:** where existing handling is consistent and pragmatic, even if not maximally elegant.

## Escalation

This skill stops at the application's boundary. Escalate when the question crosses it.

- If the failure is **between services** (sync HTTP, gRPC, internal API): load `integration-analysis`.
- If the failure is **between contexts via events** (queues, brokers, dead-letter, retries with backoff at broker level): load `event-driven-architecture`.
- If the failure is **about API contract design** (status codes, problem-details envelopes, public error vocabulary): load `api-design`.
- If the "error" is actually **a domain rule that should be modelled** (state transitions, invariants): load `ddd`.
- If the failure surfaces in **production and needs diagnosis** (alerts, dashboards, traces, structured logs): load `observability`.
- If the failure caused or threatens an **incident** (paging, on-call, runbooks, postmortems): operational; outside this skill.
- If the failure is in a **specific language's idioms** (Python, TypeScript, Go, PHP error conventions): the language review skill is supporting; this skill informs, the language skill verifies idioms.

## What this skill does NOT do

- Define cross-service failure modes. That is `integration-analysis` or `event-driven-architecture`.
- Design API error contracts. That is `api-design`.
- Pick alerting thresholds or dashboards. That is `observability`.
- Mandate exceptions or result types globally. The choice is per-codebase, per-language.
- Replace domain modelling for state transitions and invariants. That is `ddd`.
