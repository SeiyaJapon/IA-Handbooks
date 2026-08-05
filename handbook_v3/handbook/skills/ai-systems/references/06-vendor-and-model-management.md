# 06. Vendor and Model Management

## Vendor choices

Common LLM vendors:

- **OpenAI** (GPT-4 family, GPT-4o, o1, etc.).
- **Anthropic** (Claude family).
- **Google** (Gemini).
- **AWS Bedrock** (multi-vendor through one API).
- **Azure OpenAI** (OpenAI through Azure).
- **Self-hosted** (Llama, Mistral, others, on infrastructure the team manages).

Each has trade-offs in capability, cost, latency, region availability, data privacy.

## Model choice

Within a vendor, multiple models. General guidance:

- **Smallest model that meets quality.** Cost and latency improve significantly with smaller models.
- **Frontier model only when justified.** Hard reasoning, complex generation, multimodal tasks.
- **Small model for filtering.** Cheap pass to triage; large model for hard cases.

## Vendor coupling

Code coupled to one vendor's API has migration cost. Isolate:

- **API client behind a port.** The application core depends on an `LLMPort`; the adapter is vendor-specific.
- **Prompts owned by the application.** Not embedded in the vendor SDK.
- **Response parsing in the adapter.** The core sees domain-shaped results.

This is hexagonal applied to AI integration. See `ai-integration/`.

## Model versions

Vendors deprecate models. New versions release with potentially different behaviour. Plan for:

- **Pinned model version in production.** Avoid surprise behaviour changes.
- **Rotation strategy.** When deprecation forces a change.
- **Re-evaluation on rotation.** Regressions are likely.

## Multi-model strategies

Some systems use multiple models:

- **Cheap model + verification.** Generate with a small model; verify with a large model on critical paths.
- **Cascade.** Try a cheap model first; escalate to a large one if confidence is low.
- **Specialised models.** Different tasks use different models (a code model for code; a chat model for chat).

## Self-hosting

For data privacy or specific requirements, self-host:

- **Cost.** GPUs are expensive. Continuous load justifies.
- **Operational maturity.** Model serving, GPU management, scaling.
- **Capability gap.** Open models may lag frontier proprietary models.

Self-hosting is a major operational commitment.

## Anti-patterns

- **Frontier model for everything.** Cost and latency unnecessary for many tasks.
- **Vendor coupling without isolation.** SDK calls directly in business code.
- **No model version pinning.** Vendor updates change behaviour silently.
- **No rotation plan.** Deprecation forces a fire-drill migration.
- **Self-hosting without operational capacity.** GPU machines underused or breaking.

## Output

For each AI system:

- Which vendor and model?
- Why this choice?
- How is the coupling isolated?
- Is the model version pinned?
- What is the rotation plan?
