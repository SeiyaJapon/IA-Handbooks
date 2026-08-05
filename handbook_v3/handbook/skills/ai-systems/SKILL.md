# AI Systems Skill

Use this skill when discussing, designing, reviewing, or operating systems that use AI/ML, LLMs, prompts, retrieval, agents, or model evaluation.

This skill is the entry point for everything AI-related. It does not replace specific AI skills (`ai-integration`, `ai-agents`, `prompt-engineering`, `rag-design`, `llm-ops`, `ml-review`). It governs how those skills are reached and used together.

## What this skill is for

- Decide whether AI is the right answer for a problem.
- Pick which AI discipline applies (integration, agents, prompts, RAG, ops, ML).
- Coordinate decisions across AI sub-disciplines.
- Review an AI system as a whole, before drilling into specifics.

When the case is inside one specific AI skill, load that directly. This skill is for cross-AI decisions.

## Non-negotiable rule

Never start AI work from "let's use GPT / Claude / a model".

The mandatory order is:

1. Identify the **problem**. AI is one tool among many; not every problem needs AI.
2. Identify the **forces**: data availability, accuracy requirements, latency budget, cost, vendor constraints.
3. Decide AI vs non-AI. A simple deterministic solution often beats AI.
4. If AI: classical ML, LLM, or both?
5. Decide the operational model: in-house model, vendor model, hybrid.
6. Decide the interaction shape: API call (integration), prompt (LLM), agent (multi-step), RAG (retrieval-augmented).
7. Plan evaluation, monitoring, cost, and safety.
8. Only then discuss specific platforms (OpenAI, Anthropic, AWS Bedrock, etc.) and prompts.

## Hard rules

### AI is one tool

AI is not a default. A deterministic algorithm, a heuristic, a rule engine, a search query may solve the problem better, cheaper, faster, more predictably.

### Do not call a system an "agent" just because it calls an LLM

A single prompt + model API call is not an agent. A workflow with deterministic steps and one or more LLM calls is not automatically an agent. Use `ai-agents` only when the system has autonomous or semi-autonomous control flow, tool use, planning, memory, or action selection. Most cases route to `ai-integration` (single call), to a workflow skill (`event-driven-architecture`, `data-pipeline-architecture`), or to `rag-design` (retrieval), not to `ai-agents`.

### Evaluation is part of the design

An AI system without evaluation is opinion. Define how to measure quality, what acceptable looks like, how to detect regressions. Do not optimise prompts before defining evaluation criteria.

### Cost and latency are first-order

LLM calls cost money and take seconds. Both must be designed in.

### Safety is part of the architecture

Hallucinations, prompt injection, data leakage, biased outputs. Safety mitigations are part of the design.

### Vendor lock-in is real

Specific models, specific APIs, specific token formats. Coupling code to vendor specifics has migration cost.

## Forbidden shortcuts

- "Use the latest model and it will work."
- "AI is the modern way."
- "Prompts are easy; we will iterate."
- "Evaluation is for later."
- "Cost will scale with usage; we will deal with it."
- "It calls an LLM, so it is an agent."
- "We use RAG because we need context." (RAG is for retrieval and grounding, not for any context need.)

## When NOT to use AI

- Do not use AI when **deterministic code, rules, search, database queries, templates, or conventional automation** solve the problem more reliably.
- Do not use **RAG** when the problem is not knowledge retrieval or grounding.
- Do not use **agents** when a workflow, state machine, queue consumer, or normal service orchestration is enough.
- Do not use **prompt engineering** as a substitute for product, data, architecture, or evaluation work.
- Do not optimise prompts before defining evaluation criteria.
- Do not use AI when **accuracy must be 100%** (financial calculations, compliance gates, legally binding outputs).
- Do not use AI when **latency or cost budgets** are incompatible with current vendor offerings and self-hosting is out of scope.
- Do not use AI when **data privacy** rules out vendor APIs and self-hosting is not supported by the team.

## Mandatory review behavior

When reviewing an AI system:

1. Is AI justified for this problem?
2. Are evaluation, monitoring, cost, safety designed in?
3. Is the integration with the model isolated (hexagonal-like) so the model can be replaced?
4. Are prompts versioned and tested?
5. If RAG: is the retrieval evaluated separately from the generation?
6. If agent: are tools, memory, and termination conditions explicit?
7. Is there a fallback for AI failures (timeout, unavailable, low confidence)?

## When to pick AI

- Problems with natural language, images, or unstructured data.
- Tasks that humans do well but rules struggle (classification, summarisation, intent extraction).
- Workflows where flexibility outweighs determinism.

## When NOT to pick AI

- Problems with deterministic answers (use rules).
- Problems where accuracy must be 100% (AI is probabilistic).
- Problems where cost or latency makes AI infeasible.
- Problems where the data does not exist or is not usable.

## References

- `references/01-what-is-an-ai-system.md` for AI vs non-AI, types of AI.
- `references/02-when-to-use-ai.md` for the decision process.
- `references/03-ai-disciplines-overview.md` for the catalogue (integration, agents, prompts, RAG, ops, ML).
- `references/04-evaluation-and-monitoring.md` for measuring AI quality in production.
- `references/05-cost-latency-and-safety.md` for first-order operational concerns.
- `references/06-vendor-and-model-management.md` for choosing models, isolating vendor coupling.
- `references/07-anti-patterns.md` for cross-AI failures.
- `references/08-review-checklist.md` for reviewing AI systems.
- `references/09-user-preferences.md` for Francisco's project conventions.

## Sub-skills

- `ai-integration/`: integrating an LLM API into a system.
- `ai-agents/`: agent design (tools, memory, multi-step).
- `prompt-engineering/`: prompts as a design artefact.
- `rag-design/`: retrieval-augmented generation.
- `llm-ops/`: LLM operations, versioning, evaluation, cost.
- `ml-review/`: classical ML, model evaluation, feature engineering.
