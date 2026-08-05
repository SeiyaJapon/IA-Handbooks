# 05. Cost, Latency, and Safety

AI systems have first-order operational concerns that traditional software does not. Designing them in is non-negotiable.

## Cost

LLM calls cost money per token. At scale, cost can dwarf compute and infrastructure costs.

### Cost mitigations

- **Model choice.** Smaller models cost less per call. Use the smallest model that meets quality.
- **Prompt optimisation.** Shorter prompts cost less. Concise instructions, fewer examples.
- **Caching.** Cache common requests. Retrieval-augmented systems cache embeddings.
- **Batching.** Batch requests where the API allows.
- **Rate limiting.** Cap calls per user, per session, per time window.
- **Tiered models.** Use a small model for filtering, a large model for hard cases.

### Cost monitoring

- Cost per call.
- Cost per user / session / feature.
- Aggregated spend, alerted on spikes.

## Latency

LLM calls take seconds. Some tasks (long generation, reasoning) take longer. Latency must be designed into the user experience.

### Latency mitigations

- **Streaming.** Show output as it arrives instead of waiting for completion.
- **Parallel calls.** Where independent, run in parallel.
- **Smaller models for fast paths.** Same as cost.
- **Async work.** Move slow operations off the request path.
- **Caching.** Avoid repeated calls.
- **Provisioned throughput.** Some platforms allow paying for guaranteed capacity.

### Latency monitoring

- Per-call latency (p50/p95/p99).
- End-to-end latency from user request to response.
- Budget per call documented.

## Safety

AI systems have failure modes specific to AI: hallucinations, prompt injection, biased output, data leakage, harmful content.

### Hallucinations

The model invents facts. Mitigations:

- Constrain to known sources (RAG with citations).
- Validate outputs against authoritative data.
- Tell users the output is generated; require review for high-stakes decisions.

### Prompt injection

User input crafted to override the system prompt. Mitigations:

- Treat user input as untrusted; do not let it override instructions.
- Separate instructions from user input clearly.
- Use models with stronger instruction-following.
- Output validation.

### Bias

Models reflect biases in training data. Mitigations:

- Evaluate on diverse inputs.
- Monitor for biased outputs in production.
- Disclose when AI is used.

### Data leakage

Sensitive data sent to vendor APIs may be logged or used for training. Mitigations:

- Use vendor APIs with strict privacy guarantees (Bedrock, Azure OpenAI with data residency).
- Self-host for highly sensitive data.
- Redact sensitive fields before sending.

### Harmful content

Models can generate harmful content (toxic, illegal, dangerous). Mitigations:

- Use models with safety training.
- Apply content moderation on outputs.
- Block known unsafe queries.

## Anti-patterns

- **No cost budget.** Spend grows; nobody notices until the bill.
- **No streaming on long generations.** Users wait staring at a spinner.
- **Trust user input.** Prompt injection works.
- **No output validation.** Hallucinations reach production.
- **Sending sensitive data without thought.** Privacy and compliance issues.

## Output

For each AI system:

- What is the cost budget per call, per user, per day?
- What is the latency budget?
- What safety mitigations are in place?
- How are they monitored?
