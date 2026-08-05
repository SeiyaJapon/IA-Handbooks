# 02. When to Use AI

AI is one tool among many. The decision to use AI is itself an architectural decision.

## Decision process

1. **Define the problem.** Specifically, in business terms.
2. **Consider non-AI alternatives.** Rule engine, deterministic algorithm, lookup table, search query, heuristic.
3. **Evaluate AI fit.** Does the problem have natural language, patterns, ambiguity that AI handles well?
4. **Estimate cost and latency.** Per-call cost, expected volume, latency tolerance.
5. **Estimate accuracy needs.** How wrong can AI be? What does a wrong answer cost?
6. **Assess data and privacy.** Does the data exist? Can it leave the system?
7. **Decide.** Document the reason.

## When AI fits

- **Unstructured input.** Natural language, images, free-form text.
- **Pattern recognition.** Classification, summarisation, intent extraction.
- **Flexibility over determinism.** A useful answer most of the time beats a perfect answer never.
- **Augmenting human work.** AI surfaces options; humans decide.

## When AI does not fit

- **Deterministic answers required.** A calculator. A query.
- **100% accuracy required.** Compliance, financial calculations, life-critical operations.
- **Tight latency.** Real-time control loops, sub-second responses where LLMs cannot fit.
- **High volume, cost-sensitive.** AI per request at scale is expensive.
- **Privacy-sensitive data.** Vendor APIs send data out; self-hosting is expensive.

## Common bad uses

- **AI as a parser.** Extracting structured data from structured input. A regex or schema validator does it better.
- **AI for math.** LLMs are unreliable at arithmetic. Use a calculator.
- **AI for known logic.** Business rules that can be expressed in code. Code is more predictable.
- **AI as a replacement for missing requirements.** "We do not know what to do, let AI decide." This produces erratic systems.

## Common good uses

- **Natural language understanding.** Intent extraction, summarisation, classification of text.
- **Generation.** Drafting text, code, suggestions.
- **Pattern detection.** Anomaly detection, content moderation, recommendations.
- **Augmentation.** A human reviews AI output before acting.

## Hybrid approaches

Often the right answer is a mix:

- AI for the unstructured part (parse natural language).
- Code for the deterministic part (validate, calculate, decide).
- AI for the suggestion; human for the decision.

Pure AI systems are rare and often the wrong shape.

## Output

For each AI use case, the team can answer:

- What is the problem?
- What did the team consider as non-AI alternative?
- Why is AI the right tool?
- What accuracy is acceptable?
- What is the cost and latency budget?
- What happens when AI is wrong?
