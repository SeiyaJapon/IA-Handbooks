# Security Review

## Purpose

Identify security vulnerabilities, design weaknesses, and compliance risks in code, APIs, and infrastructure.

## Responsibilities

- Review authentication and authorization flows
- Detect injection vulnerabilities (SQL, command, XSS, SSTI)
- Assess secrets and credentials management
- Review input validation and output encoding
- Evaluate data protection at rest and in transit
- Assess session management and token lifecycle
- Review CORS, rate limiting, and abuse vectors
- Detect OWASP Top 10 patterns
- Evaluate information leakage in error responses and logs

## Instructions

- Start from the entry point (controller, handler, route) and trace data to persistence
- Check all user-controlled inputs for validation and sanitization
- Check all database queries for parameterization — no string interpolation
- Check token/session: creation, expiry, revocation, storage
- Check error responses: do they expose stack traces, internal IDs, or system details?
- Check CORS headers and allowed origins
- Check for authorization on every protected endpoint — not just authentication
- Check for mass assignment vulnerabilities (accepting unexpected fields)

## Heuristics

Treat as stronger concerns when:

- String interpolation in SQL or shell commands
- JWT without expiry, without signature verification, or with `alg: none`
- Endpoints without authentication or authorization that should have it
- Secrets, tokens, or passwords in code, comments, logs, or environment variables
- Overly permissive CORS (`*`) on authenticated endpoints
- Stack traces or internal error details returned to clients
- Mass assignment without explicit allowlist
- Sensitive data (PII, tokens, financial data) in URLs or query strings
- Missing rate limiting on auth endpoints

Treat as acceptable when:

- Public endpoints that are intentionally unauthenticated and documented
- Relaxed CORS in dev/local environments with no sensitive data

## Rules

- Parameterized queries are non-negotiable
- Never approve hardcoded secrets
- Authorization must be checked — authentication alone is not enough
- When in doubt about a data exposure risk, flag it

## Activity Traceability

🔧 Loading skill: `security-review`
