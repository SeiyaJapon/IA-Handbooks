# 09. User Preferences (Francisco)

Project conventions for AI systems in Francisco Pérez's projects at Kintai.

## When AI is acceptable

AI is acceptable when:

- The problem genuinely fits AI's strengths (unstructured input, language, patterns).
- A non-AI alternative was considered and ruled out for documented reasons.
- Cost, latency, accuracy budgets are defined and acceptable.
- Evaluation is part of the design.

## When AI is rejected

- Deterministic problems (use code).
- 100% accuracy requirements.
- High volume + cost-sensitive without strong fit.
- Sensitive data without privacy guarantees.

## Default vendor

Default backend AI vendor: **Anthropic Claude** (via API or AWS Bedrock).

OpenAI, Google Gemini are alternatives chosen per case.

Self-hosting is reserved for specific privacy or control needs.

## Default models

- **Reasoning, complex generation:** Claude Opus or Sonnet (current frontier).
- **Filtering, classification, simple generation:** smaller Sonnet or Haiku.
- **Embeddings:** Voyage, Cohere, or OpenAI embeddings.

Smallest model that meets quality.

## Prompt management

- Prompts are versioned in the repo.
- Prompts are tested with a golden dataset.
- Prompt changes go through review like code.

## Vendor isolation

- LLM API access is behind a port.
- The port lives in `application/`; the adapter in `infrastructure/`.
- Business code does not import vendor SDKs directly.

## Evaluation

- Golden dataset for each AI feature.
- Continuous evaluation in production samples.
- Regression detected on prompt or model changes.

## Cost and latency

- Cost budget per feature, monitored.
- Latency budget per request, monitored.
- Streaming on long generations.
- Caching where the same input recurs.

## Safety

- Hallucination mitigation: RAG with citations where possible; output validation against authoritative data.
- Prompt injection mitigation: separate user input from instructions; output validation.
- Privacy: sensitive data not sent to vendor APIs without classification and redaction.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the canonical evaluation tooling is custom, LangSmith, Weights & Biases, or similar.
- Whether prompts are stored as TS strings, YAML, or a dedicated prompt store.
- Whether AI features in this project use streaming by default or only where the UI demands it.

These are open. Do not assume; ask.
