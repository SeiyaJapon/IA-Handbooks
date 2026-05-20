# Error Handling Patterns

## Purpose

Review the design of error handling across a codebase: error types, propagation strategy, boundaries, and consistency.

## Responsibilities

- Evaluate error type hierarchy and domain error design
- Review error propagation: where errors are caught, transformed, and surfaced
- Assess error boundaries and recovery strategy
- Detect silent failures and swallowed errors
- Evaluate error contracts at API and service boundaries
- Review error context and diagnostic information
- Assess consistency of error handling across the codebase

## Instructions

- Identify the error handling strategy: exceptions, result types, error callbacks, or mixed
- Check error types: are domain errors distinguishable from infrastructure errors?
- Check propagation: are errors caught at the right layer, not too early or too late?
- Check error boundaries: is there a top-level handler that catches unhandled errors and logs them?
- Check context: does the error carry enough information to diagnose without a debugger?
- Check consistency: is the same error translated to different HTTP status codes in different places?
- Check re-throw: when an error is caught and re-thrown, is context preserved?
- Check client-facing errors: is internal detail stripped? is the message actionable?

## Heuristics

Treat as stronger concerns when:

- Empty catch blocks (`catch {}`, `catch (e) {}` with no log or re-throw)
- Generic `Error` thrown everywhere — no distinction between domain, validation, and infrastructure errors
- Error caught and swallowed at a low layer — propagation lost, caller sees success
- Stack trace or internal error detail returned to API clients
- Same error condition handled differently in different controllers
- Error context lost on re-throw (`throw new Error('failed')` discards original stack)
- Errors logged multiple times at different layers — noise in observability

Treat as acceptable when:

- Simple scripts with minimal error handling when failure is terminal and obvious
- Catching a specific error to apply a known recovery strategy without logging

## Rules

- Silent failure is never acceptable — every catch must log, re-throw, or recover explicitly
- Domain errors must be distinguishable from infrastructure errors at the boundary
- Client-facing error responses must never expose internal details
- Error context must survive propagation — wrap with cause, not replace

## Activity Traceability

🔧 Loading skill: `error-handling-patterns`
