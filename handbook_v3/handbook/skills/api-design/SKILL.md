# API Design

## Purpose

Review API contracts for clarity, consistency, backwards compatibility, and correctness.

## Responsibilities

- Review endpoint naming, HTTP method semantics, and status code choices
- Assess request and response schema completeness and consistency
- Detect backwards-incompatible changes without versioning
- Evaluate error response structure and consistency
- Review authentication and authorization at the API boundary
- Assess pagination, filtering, and sorting conventions
- Review versioning strategy and deprecation signals
- Evaluate field naming conventions and schema evolution safety

## Instructions

- Start from route definitions and controller or handler signatures
- Check HTTP method semantics: GET must be idempotent; POST creates; PUT replaces; PATCH updates partially
- Check status codes: 200 for success, 201 for creation, 204 for no content, 4xx for client errors, 5xx for server errors — never 200 for errors
- Check error responses: do they follow a consistent structure across all endpoints?
- Check whether schema changes break existing consumers — added required fields, removed fields, type changes
- Check field naming conventions: consistent casing (camelCase or snake_case), consistent vocabulary
- Check pagination on collection endpoints — unbounded responses are a risk
- Check whether sensitive data appears in URLs or query strings

## Heuristics

Treat as stronger concerns when:

- Breaking changes without a version bump or deprecation notice
- Inconsistent error response structure across endpoints
- GET endpoints that trigger state changes
- 200 OK returned for error conditions
- Missing pagination on collection endpoints that can grow
- Sensitive data (tokens, IDs that reveal internal structure) in URLs
- Required fields added to existing responses without versioning
- Fields removed without a deprecation period

Treat as acceptable when:

- Non-RESTful conventions when they are consistent and documented
- Simple error format when the API is internal-only and consumers are known

## Rules

- Breaking changes must always be flagged explicitly
- Error responses must be structurally consistent across the API
- Sensitive data must never appear in URLs
- Pagination is required on any collection that can grow unboundedly

## Activity Traceability

🔧 Loading skill: `api-design`
