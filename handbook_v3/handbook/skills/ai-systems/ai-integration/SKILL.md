# AI Integration Skill

Integrate an LLM (or other model) API into a software system: API client design, error handling, retries, streaming, prompt formatting, response parsing, fallback. This is the plumbing of AI into a backend.

## When to use

Use this skill when:

- A system **calls an LLM API** for one or more operations.
- The interaction is a **single call** or a small fixed sequence of calls, not autonomous control flow.
- The question is about **retries, streaming, parsing, validating, fallback, cost, latency** of LLM calls.
- The integration must be **isolated from vendor specifics** (port + adapter shape).

## When not to use

Do not use this skill when:

- The system has **autonomous control flow** with tools and memory. Use `ai-agents`.
- The work is **prompt design** (clarity, structure, evaluation). Use `prompt-engineering`.
- The work is **retrieval** for grounding. Use `rag-design`.
- The work is **operating the system in production** (eval, drift, model rotation, cost dashboards). Use `llm-ops`.
- The question is whether **AI is the right tool**. Use `ai-systems` (the mega-skill).

## Inputs to inspect first

Before reviewing or designing AI integration, inspect:

- **The vendor and model.** Which one, which version, why pinned (or not).
- **The call site.** Where in the code does the LLM get called? Synchronous request path, async worker, batch?
- **The port/adapter shape.** Is there a port defined by the application, with a vendor-specific adapter?
- **Prompts.** Are they versioned in the repo, or hardcoded inline?
- **Response parsing.** Is the response validated? What happens on malformed output?
- **Failure modes.** Timeouts, rate limits, vendor downtime, model deprecation. What is the fallback?
- **Cost monitoring.** Per-call, per-feature, alerts.
- **Privacy.** Is PII or sensitive data going to the vendor?

## Hard rules

- **Vendor isolation.** The application core defines a port; the vendor SDK lives in an adapter. Replacing the vendor must be bounded to the adapter.
- **Pinned model versions.** Vendor updates change behaviour. Pin and rotate deliberately.
- **Validated outputs.** LLM responses are not trusted as structured data. Parse, validate, defend.
- **Bounded retries.** Transient failures retried with backoff, capped count.
- **Bounded latency.** Synchronous request paths have a timeout; the user does not wait forever.
- **Fallbacks designed in.** When the model is unavailable, slow, or low-confidence, the system has a path: cached answer, simpler heuristic, polite error.
- **Cost guardrails.** Per-user, per-day caps on high-volume features. Alerts on spikes.
- **PII discipline.** Sensitive data classified before being sent. Redaction or self-hosting where required.
- **Streaming when latency matters.** Long generations stream to the UI; users do not stare at a spinner.

## How to work

1. **Confirm the integration is a single call or a small fixed sequence**, not an agent loop.
2. **Inspect the inputs** above.
3. **Check vendor isolation.** Direct SDK usage in business code is a violation; isolate.
4. **Check version pinning and rotation plan.**
5. **Check response handling.** Schema validation, fallback, error mapping.
6. **Check failure handling.** Timeout, retry, circuit breaker, degraded path.
7. **Check cost and latency.** Budgets defined and monitored; smallest model that meets quality.
8. **Check privacy.** Data classified; sensitive paths redacted or self-hosted.
9. **Recommend the smallest change** that closes the active risk.

## Output

Return findings as:

- Concrete violations with file/class references.
- Recommended changes (port extraction, schema validation, retry config, fallback path, cost guardrail).
- Architecture or operations escalations when the issue is broader than one integration.

## Escalation

- Prompt design quality, evaluation, structured output: `prompt-engineering`.
- Autonomous control flow with tools: `ai-agents`.
- Retrieval and grounding: `rag-design`.
- Production operations (eval, drift, cost dashboards, model rotation): `llm-ops`.
- Whether AI is the right tool at all: `ai-systems` (mega-skill).
- The integration crosses service boundaries (one service calls another that wraps an LLM): also `integration-analysis`.
- Privacy or compliance concerns: `security-review` and `compliance-patterns`.

## Operational checks (legacy, kept as a checklist)

When auditing an integration, walk these signals:

- User input injected into system prompts without sanitisation (prompt injection).
- Model output parsed as trusted structured data without validation.
- Unbounded context accumulation per session.
- PII or sensitive business data sent to external LLM APIs without review.
- Synchronous LLM call in a request path with strict latency requirements.
- No cost guardrails on high-volume or user-triggered calls.
- Hard dependency on a single model version without a rotation path.
- No fallback when the model is unavailable.
