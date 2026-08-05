# 03. AI Disciplines Overview

The AI sub-skills covered in this handbook.

## ai-integration

Integrating an LLM (or other model) API into a system.

Topics: API client design, error handling, retries, streaming, prompt formatting, response parsing.

When to load: when the question is about how to call an LLM from code reliably.

## ai-agents

Designing AI agents: systems where the model plans, calls tools, maintains memory, takes multi-step actions.

Topics: tool design, memory, termination, error handling, multi-agent systems, observability.

When to load: when the system uses an LLM in a loop with tool calls or multi-step reasoning.

## prompt-engineering

Prompts as a design artefact.

Topics: structure, instructions, few-shot, chain-of-thought, structured output, prompt versioning, evaluation.

When to load: when the question is about prompt design or improvement.

## rag-design

Retrieval-augmented generation: combining a retrieval system (often embeddings + vector search) with an LLM.

Topics: chunking, embedding choice, retrieval evaluation, context window management, evaluation of generation quality.

When to load: when the AI system retrieves information from a corpus before generating.

## llm-ops

LLM operations: model versioning, evaluation, cost monitoring, A/B testing, drift detection.

Topics: evaluation harnesses, cost dashboards, model rotation, regression detection.

When to load: when the question is about operating an LLM-based system in production.

## ml-review

Classical ML: model evaluation, feature engineering, training pipelines, deployment.

Topics: training/serving skew, feature stores, model monitoring, retraining triggers.

When to load: when the question is about classical ML, not LLMs.

## How they compose

Many AI systems use several disciplines:

- A RAG system uses `rag-design` (retrieval), `prompt-engineering` (the prompt), `ai-integration` (the LLM call), `llm-ops` (evaluation, cost).
- An agent uses `ai-agents` (the loop), `prompt-engineering` (each prompt), `ai-integration` (each call), `llm-ops` (operating).
- A classification system using a fine-tuned classical model uses `ml-review` (model) and possibly `ai-integration` (serving).

Loading one sub-skill does not preclude loading another. The composition is deliberate.

## Output

For each AI system, the team can answer:

- Which disciplines apply?
- Which are loaded for design?
- Which are loaded for review?
