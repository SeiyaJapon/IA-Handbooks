# 04. Evaluation and Monitoring

An AI system without evaluation is opinion. Designing evaluation in is non-negotiable.

## Why evaluation is hard

AI systems are probabilistic. The same input may produce different outputs. Quality is not binary; it is a distribution.

Traditional testing (unit tests, integration tests) covers code paths; it does not cover model quality. AI evaluation is its own discipline.

## What to evaluate

- **Accuracy.** Does the system produce correct answers?
- **Relevance.** Are the answers on-topic?
- **Faithfulness.** For RAG, does the answer reflect the retrieved sources?
- **Format.** Does the answer match the expected structure?
- **Latency.** How long does it take?
- **Cost.** How much does each call cost?
- **Safety.** No harmful, biased, or sensitive outputs.

## Evaluation methods

### Golden dataset

A curated set of inputs with known good answers (or rubrics for quality). Run the system; compare outputs to the dataset.

Pros: repeatable, regression-detectable.

Cons: dataset curation is expensive; representative coverage is hard.

### LLM-as-judge

Use an LLM (often a stronger one) to score outputs of the system under test against a rubric.

Pros: scales evaluation; works for subjective quality.

Cons: judge LLMs have biases; requires careful prompt design.

### Human evaluation

Human raters score outputs.

Pros: most reliable.

Cons: expensive, slow, hard to scale.

### A/B testing in production

Different versions serve different traffic; compare metrics.

Pros: real-world signal.

Cons: requires production traffic; latency to detect regressions.

## Monitoring in production

- **Cost per call.** Aggregated, alerted on spikes.
- **Latency.** Per call, p50/p95/p99.
- **Error rate.** API errors, timeouts, parse failures.
- **Quality metrics.** From production samples, evaluated continuously.
- **Drift.** Inputs changing over time; outputs changing in response.

A production AI system without these monitors is invisible until it breaks.

## When to re-evaluate

- New model version deployed.
- New prompt version deployed.
- Input distribution changes (new use cases).
- Cost or latency spikes.
- User feedback indicates regression.

## Anti-patterns

- **No evaluation harness.** Quality known only when users complain.
- **Manual testing as evaluation.** A few examples checked once; not repeatable.
- **No monitoring in production.** Cost spikes, quality drift go unnoticed.
- **Evaluation only at deployment.** Drift detected late.
- **No baseline.** "Improvements" are not measured; opinions are.

## Output

For each AI system, the team can answer:

- What does evaluation measure?
- What is the golden dataset (or equivalent)?
- How is regression detected?
- What is monitored in production?
- Who reviews evaluation results?
