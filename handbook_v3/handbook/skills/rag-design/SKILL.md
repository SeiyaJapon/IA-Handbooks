# RAG Design

## Purpose

Review Retrieval-Augmented Generation (RAG) system design for retrieval quality, architecture correctness, and operational soundness.

## Responsibilities

- Evaluate chunking strategy and its effect on retrieval quality
- Assess embedding model selection and consistency
- Review vector store choice and index configuration
- Evaluate retrieval pipeline: query transformation, similarity search, reranking
- Assess context assembly and prompt construction from retrieved chunks
- Review evaluation strategy for retrieval and generation quality
- Evaluate update and re-indexing strategy for the knowledge base
- Detect hallucination risk from poor retrieval or context overflow

## Instructions

- Identify the full pipeline: document ingestion → chunking → embedding → index → retrieval → context assembly → generation
- Check chunking: are chunks sized for the embedding model's context window? do they preserve semantic coherence?
- Check embedding model: is it the same model used at index time and query time?
- Check retrieval: is similarity threshold tuned? is there a fallback for no results?
- Check reranking: is it used when recall matters more than speed?
- Check context assembly: is the retrieved context ordered and trimmed to fit the prompt?
- Check evaluation: is there a baseline to measure retrieval precision and recall?
- Check re-indexing: when documents change, is the index updated or rebuilt?

## Heuristics

Treat as stronger concerns when:

- Different embedding models used at ingestion and query time
- Chunk size exceeding the embedding model's effective context window
- No fallback when retrieval returns no results
- Context window overflow — retrieved text truncated silently
- No evaluation baseline — no way to detect retrieval degradation
- Knowledge base updated without triggering re-indexing
- Metadata filtering not used when documents have clear categorical boundaries
- Retrieved chunks assembled without relevance ordering

Treat as acceptable when:

- No reranking in low-volume or low-criticality internal RAG tools
- Manual re-indexing when the knowledge base changes infrequently

## Rules

- Embedding model must be consistent across ingestion and retrieval
- Retrieval must handle the no-result case explicitly
- Context assembly must respect the model's context window — never truncate silently
- Evaluation baseline is required before any production deployment

## Activity Traceability

🔧 Loading skill: `rag-design`
