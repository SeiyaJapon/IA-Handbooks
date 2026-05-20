# AI Integration

## Purpose

Review the design, implementation, and operational considerations of AI and LLM integrations.

## Responsibilities

- Evaluate model selection and fit for the use case
- Review prompt design for clarity, safety, and robustness
- Assess token usage, cost estimation, and caching strategy
- Detect prompt injection risks
- Evaluate fallback and error handling when the model fails or times out
- Review output parsing and validation before use
- Assess context window management and truncation handling
- Evaluate latency impact on user-facing flows
- Review PII and sensitive data handling in prompts and responses

## Instructions

- Start from prompt templates and model invocation code
- Check prompt structure: system role, user role, examples — is the intent unambiguous?
- Check for user-controlled input injected into system prompts without sanitization
- Check whether the model response is validated before being used or displayed
- Check token limits: is there a truncation strategy? what happens when context is exceeded?
- Check caching: prompt caching and result caching where applicable
- Check fallback behavior: what happens when the model is unavailable, slow, or returns unexpected output?
- Check for PII in prompts sent to external APIs
- Check whether LLM calls are in synchronous, latency-sensitive paths

## Heuristics

Treat as stronger concerns when:

- User input injected directly into system prompts without sanitization (prompt injection)
- No fallback when the model is unavailable or returns an error
- Model output parsed as trusted structured data without validation
- Unbounded context accumulation per session (growing context window)
- PII or sensitive business data sent to external LLM APIs without review
- Synchronous LLM call in a request path with strict latency requirements
- No cost guardrails on high-volume or user-triggered model calls
- Hard dependency on a single model version without a migration path

Treat as acceptable when:

- Simple prompt without few-shot examples when the task is low-risk and unambiguous
- No caching in development or low-volume internal tooling

## Rules

- Always check for prompt injection vectors before any user input reaches a prompt
- Always check fallback and error handling
- PII in prompts sent to third-party APIs requires explicit review
- Flag LLM calls in synchronous paths without latency justification

## Activity Traceability

🔧 Loading skill: `ai-integration`
