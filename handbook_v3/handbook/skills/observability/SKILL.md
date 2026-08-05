# Observability Skill

## When to use

The user asked about logs, metrics, traces, alerts, dashboards, SLIs/SLOs, structured logging, distributed tracing, production diagnosis.

## When not to use

- Application error handling at the function/module level: `error-handling-patterns`.
- AI-specific operational concerns (eval, drift, cost dashboards for LLMs): `llm-ops`.
- Alerting policy and runbooks (operational): outside the skill set; surfaces here informs.
- Generic security posture: `security-review`.

## Inputs to inspect first

- Logging library and shape (structured? plain text?). Sampling and verbosity per environment.
- Metrics library and backend (CloudWatch, Datadog, Prometheus). Cardinality of labels.
- Tracing: instrumented or not; vendor; correlation IDs propagated end-to-end.
- Dashboards in use; freshness; ownership.
- Alert policies: thresholds, recipients, runbook links.
- SLIs/SLOs if any; error budget.

## How to work

1. Inspect logging, metrics, tracing setup.
2. Walk concerns: structured logs, low-cardinality metrics, end-to-end tracing, alert hygiene, dashboard ownership.
3. Group findings by severity.

## Output

Findings grouped: blockers (no alerts on critical paths, secrets in logs, unbounded label cardinality), defects (unstructured logs, partial tracing, missing correlation IDs), nits (dashboard naming).

## Escalation

- AI-specific drift, cost, model rotation: `llm-ops`.
- Errors at the application level: `error-handling-patterns`.
- Security signals (auth failures, suspicious activity): `security-review`.
- Compliance audit trails: `compliance-patterns`.
- Performance regressions at the system level: `performance-analysis`.

---

## Purpose

Review logging, metrics, tracing, and alerting design to ensure the system is observable and diagnosable in production.

## Responsibilities

- Review log level choices and structured logging format
- Assess what is logged, what is missing, and what is excessive
- Evaluate metric instrumentation and naming conventions
- Review alerting coverage and threshold rationale
- Assess distributed tracing integration and correlation IDs
- Detect sensitive data (PII, tokens, secrets) in logs
- Evaluate log retention policy and cost implications
- Review error reporting and aggregation setup

## Instructions

- Start from the changed code and identify what new behavior is introduced
- For each significant operation: is there a log entry at start, end, and on failure?
- For each error: is it logged with enough context to diagnose without a debugger?
- For each new external call: is there a metric for latency and error rate?
- For each new critical path: is there an alert for failure modes?
- Check for PII, tokens, or secrets in log messages
- Check for correlation IDs or request context in all log entries within a request scope
- Check log levels: debug for internal state, info for significant events, warn for recoverable issues, error for failures

## Heuristics

Treat as stronger concerns when:

- Errors caught without any log entry
- PII, passwords, or tokens in log messages
- New external service calls without latency or error rate metrics
- No alert for a new critical or user-facing path
- Log entries without correlation ID or request context
- Debug-level verbosity in production code paths
- Missing structured fields — log messages that are only free-text strings in a structured logging system
- Logs that grow unboundedly without retention or rotation policy

Treat as acceptable when:

- Missing metrics on non-critical internal operations in early development
- Simple log message without structured fields for genuinely trivial operations

## Rules

- Errors must always be logged with context — catch without log is never acceptable
- PII in logs is never acceptable
- New critical or user-facing paths require at least one alert
- Correlation IDs must propagate across async boundaries

## Activity Traceability

🔧 Loading skill: `observability`
