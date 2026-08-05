# RAG Design Skill

Design and review retrieval-augmented generation: **chunking, indexing, retrieval, ranking, grounding, citations, freshness, retrieval evaluation**. RAG is for grounding an LLM in a corpus that it cannot or should not memorise.

## When to use

Use this skill when:

- The system **retrieves documents from a corpus** and grounds an LLM's output in them.
- The user asks for **chunking, embedding, indexing, similarity search, reranking, citations**.
- Hallucinations are surfacing because the model is generating without sources.
- Knowledge freshness or coverage is a concern.
- A retrieval evaluation harness is being designed.

## When not to use

Do not use this skill when:

- The system **does not need retrieval**. Most LLM use cases do not. RAG is for knowledge grounding, not for "give the model some context".
- The work is **prompt design** (instructions, format, examples). Use `prompt-engineering`. Prompts in a RAG system live downstream of retrieval, but the prompt itself is a separate skill.
- The work is **integrating the LLM** (clients, retries, streaming). Use `ai-integration`.
- The work is **autonomous control flow with tools**. Use `ai-agents`. RAG can be a tool an agent uses, but RAG itself is not an agent.
- The work is **production operations** (eval drift, cost, retrieval latency dashboards). Use `llm-ops`.
- The "context" is short, fixed, and known at design time. Put it in the prompt; this is not RAG.

## Inputs to inspect first

Before reviewing or designing a RAG system, inspect:

- **The corpus.** What documents? Size, format, freshness, ownership.
- **The query distribution.** What questions does the system answer?
- **The full pipeline.** Ingestion, chunking, embedding, index, retrieval, optional rerank, context assembly, generation.
- **The embedding model.** Same at index time and query time? Pinned version?
- **The vector store.** Type, index configuration, scaling profile.
- **Metadata and filtering.** Does retrieval restrict by author, date, category?
- **Evaluation.** Is there a retrieval evaluation harness (precision/recall on a labelled set)? A generation evaluation (faithfulness, citations)?
- **Re-indexing strategy.** When the corpus changes, what happens?

If retrieval is unevaluated, RAG quality is opinion.

## Hard rules

- **Evaluate retrieval separately from generation.** A correct generation over wrong retrieval is luck. A wrong generation over correct retrieval is a prompt problem. Distinguish.
- **Same embedding model at ingest and query.** Different models produce incompatible vectors.
- **Pin embedding model versions.** Vendor updates change vectors silently.
- **No silent truncation.** When context exceeds the window, truncation is explicit and prioritised; the system does not lose chunks invisibly.
- **No-result is a real case.** When retrieval returns nothing, the system has a defined behaviour (refuse, ask for clarification, fall back, alert).
- **Citations when grounding matters.** If the user must verify, surface the source. RAG without citations encourages hallucinated faithfulness.
- **Freshness is part of the design.** When the corpus changes, the index updates. Stale retrieval is a quality regression.
- **Metadata filtering when categories matter.** Vector similarity alone often retrieves topically close but legally or contextually wrong documents.

## Concerns this skill covers

- **Chunking:** size, overlap, structure-aware, semantic-aware. The chunk is the unit of retrieval; chunk well or retrieve badly.
- **Embedding:** model choice, dimensionality, normalisation, version pinning, batching at ingest.
- **Indexing:** vector store, metadata, hybrid (dense + sparse), index configuration.
- **Retrieval:** top-k, similarity threshold, hybrid search, query rewriting, multi-query, multi-step.
- **Reranking:** cross-encoder rerank when recall-then-precision is needed.
- **Grounding:** how retrieved chunks reach the prompt, in what order, with what attribution.
- **Citations:** sources surfaced to the user; the system can answer "where did this come from?".
- **Freshness and re-indexing:** triggers, incremental vs full rebuild.
- **Retrieval evaluation:** golden queries, expected results, precision/recall metrics, drift detection.

## How to work

1. **Confirm RAG is the right answer.** Many "context" needs do not need RAG.
2. **Inspect the full pipeline** end to end. Identify the weakest link first.
3. **Check evaluation.** If retrieval is unevaluated, that is the priority.
4. **Inspect chunking** for semantic coherence and embedding fit.
5. **Inspect retrieval** for top-k tuning, hybrid usage, no-result handling.
6. **Inspect reranking** if precision matters and recall is wide.
7. **Inspect grounding and citations.** Are sources surfaced? Are they faithful?
8. **Inspect freshness and re-indexing.**
9. **Recommend the smallest change** that closes the active gap.

## Output

Return findings as:

- Concrete pipeline issues at the named stage.
- Recommended changes (chunk size, embedding pin, hybrid search, rerank, citation surfacing, eval harness).
- Escalations when the issue is upstream (corpus quality, knowledge management) or downstream (prompt design, generation eval).

## Escalation

- The prompt that uses retrieved chunks: `prompt-engineering`.
- The LLM integration around the prompt: `ai-integration`.
- Autonomous use of retrieval as a tool by an agent: `ai-agents`.
- Production operations (drift, cost, latency dashboards for retrieval): `llm-ops`.
- Whether the task should use AI at all: `ai-systems`.
- Privacy or compliance of corpus contents: `security-review`, `compliance-patterns`.
- Document storage and corpus shape are themselves a `database-design` or `data-pipeline-architecture` concern when the corpus is large, structured, or pipelined.

## Operational checks (legacy, kept as a checklist)

- Different embedding models used at ingestion and query time.
- Chunk size exceeding the embedding model's effective context window.
- No fallback when retrieval returns no results.
- Context window overflow; retrieved text truncated silently.
- No evaluation baseline; no way to detect retrieval degradation.
- Knowledge base updated without triggering re-indexing.
- Metadata filtering not used when documents have clear categorical boundaries.
- Retrieved chunks assembled without relevance ordering.
