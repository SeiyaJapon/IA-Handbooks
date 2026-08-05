# 07. Anti-patterns

## AI as a default

Adopting AI before checking simpler alternatives.

Fix: deterministic alternatives first; AI where the problem genuinely fits.

## AI as a parser

Using an LLM to extract structured fields from structured input. A regex, schema validator, or specific parser does it better.

Fix: use the right tool. AI for unstructured input only.

## AI for math

LLMs are unreliable at arithmetic. Calculations belong in code.

Fix: code does math; AI invokes the code if needed.

## AI without evaluation

Quality is opinion. Regressions invisible.

Fix: golden dataset, LLM-as-judge, or human evaluation. Continuous in production.

## AI without monitoring

Cost spikes, latency drift, error rates invisible.

Fix: cost dashboards, latency dashboards, error tracking, alerts.

## Trust user input

Prompt injection works. The user's input ends up overriding system instructions.

Fix: separate user input from instructions clearly. Output validation.

## No vendor isolation

SDK calls embedded in business code. Vendor migration is a rewrite.

Fix: hexagonal-like isolation. The core depends on an LLM port; the adapter knows the vendor.

## No model version pin

Vendor updates change behaviour silently. Production regressions surface mysteriously.

Fix: pin model versions. Re-evaluate before rotation.

## Frontier model for everything

Cost and latency unnecessary for tasks a smaller model handles.

Fix: smallest model that meets quality. Use frontier only where needed.

## No fallback

When AI fails (timeout, unavailable, low confidence), the user-facing operation fails.

Fix: design fallbacks (cached answer, simpler heuristic, polite error).

## Sending sensitive data without thought

Privacy and compliance issues. Data on vendor logs.

Fix: data classification before sending. Redaction, vendor with strict privacy, or self-hosting.

## RAG without retrieval evaluation

The retriever returns wrong sources; the generator hallucinates faithfully from them.

Fix: evaluate retrieval separately from generation.

## Agent without termination conditions

The agent loops forever or takes far more steps than expected.

Fix: explicit termination conditions, step limits, cost limits.

## Output

When reviewing AI systems, look for these patterns. AI as a default, no evaluation, no monitoring, vendor coupling, and no fallback are the most common.
