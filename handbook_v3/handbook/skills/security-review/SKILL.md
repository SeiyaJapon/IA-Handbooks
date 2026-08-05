# Security Review Skill

## When to use

The user asked for a security review of code, configuration, or design. Concerns: authentication, authorisation, secrets, OWASP top 10, data protection, IAM, secure defaults.

## When not to use

- Compliance-specific requirements (audit trails, retention, regulatory): `compliance-patterns`.
- AWS IAM at infrastructure level: `aws-infrastructure`. (Security review may flag, then escalate.)
- Test strategy that verifies security paths: `testing-strategy`.

## Inputs to inspect first

- Auth model: how identity is established, how authorisation decisions are made, where they are enforced.
- Secret handling: where secrets live (env, secret manager, vault), how they are loaded, whether they reach logs or errors.
- Input handling: validation, sanitisation, escaping per output context (HTML, SQL, shell).
- Data classification: what is sensitive (PII, credentials, payment data, regulated data).
- Logging: what is logged; whether secrets/PII could leak into logs or telemetry.
- External dependencies: known CVEs, supply chain hygiene.
- Network exposure: public vs internal, TLS, mTLS where applicable.

## How to work

1. Inspect auth and authorisation enforcement points.
2. Inspect secret handling and logging paths.
3. Walk OWASP-style concerns (injection, broken auth, sensitive data exposure, XXE, broken access control, security misconfiguration, XSS, insecure deserialization, vulnerable dependencies, insufficient logging).
4. Group findings by severity (critical, high, medium, low).
5. Do not modify code.

## Output

Findings cited by file/line, grouped by OWASP category and severity. Each finding includes: the concrete vector, the evidence (file/line), the impact, the recommended mitigation.

## Escalation

- Compliance-specific posture (audit, retention, regulated data): `compliance-patterns`.
- AWS IAM design: `aws-infrastructure`.
- API contract design (auth shape, error responses): `api-design`.
- Observability for security signals: `observability`.
- Specific language idioms: the corresponding language review skill.

---

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
