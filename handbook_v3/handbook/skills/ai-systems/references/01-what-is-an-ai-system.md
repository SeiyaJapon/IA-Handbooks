# 01. What is an AI System

An AI system is software whose behaviour depends on a model: a classical machine learning model, a large language model, a vision model, an embedding model, etc.

## Types of AI

### Classical ML

Models trained on data: classification, regression, clustering, recommendation.

- Inputs and outputs structured.
- Training is a defined phase.
- Evaluation against held-out data.
- Inference is fast (milliseconds).

Skill: `ml-review/`.

### Large Language Models (LLMs)

Pre-trained models that process text (and increasingly images, audio).

- Used through APIs (OpenAI, Anthropic, Bedrock) or self-hosted.
- Inference is slower (seconds).
- Outputs are probabilistic; quality varies.
- Hallucinations are a known failure mode.

Skill: `ai-integration/` for using them; `prompt-engineering/` for the prompts.

### Vision and other modalities

Image, audio, video models. Same patterns as LLMs (vendor APIs, prompts as inputs, evaluation).

### Embeddings

Numerical representations of text or other data. Used for similarity search, retrieval, clustering.

Skill: `rag-design/` (uses embeddings for retrieval).

### Agents

Systems where an LLM (or other model) calls tools, maintains memory, takes multi-step actions.

Skill: `ai-agents/`.

## What an AI system is not

- A simple rule engine. Rules are deterministic; AI is probabilistic.
- A search engine (although search may be part of an AI system, e.g. in RAG).
- A workflow with deterministic steps (although AI may be one of the steps).

## When AI is the right tool

- Problems with natural language, images, audio.
- Pattern recognition where rules struggle.
- Tasks where flexibility matters more than perfect accuracy.

## When AI is the wrong tool

- Deterministic answers (use rules).
- Accuracy must be 100% (AI is probabilistic).
- Latency budget is tight (LLMs are slow).
- Cost matters more than capability (LLMs are expensive at scale).
- Data privacy is sensitive (vendor APIs send data out).

## Decision

Ask:

1. Is the problem suited to AI's strengths (language, patterns, flexibility)?
2. Is AI's accuracy acceptable for this use case?
3. Is the latency budget compatible?
4. Is the cost budget compatible?
5. Are safety and privacy concerns manageable?

Multiple noes: AI is not the right tool.
