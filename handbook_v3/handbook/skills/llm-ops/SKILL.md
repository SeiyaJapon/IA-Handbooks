# LLM Ops

## Purpose

Review the operational design of LLM-based systems: model lifecycle, deployment, versioning, cost management, evaluation, and production monitoring.

## Responsibilities

- Evaluate model versioning and upgrade strategy
- Assess prompt versioning and regression testing approach
- Review cost monitoring and budget controls
- Evaluate rate limit handling and throttling strategy
- Assess production evaluation and quality monitoring
- Review model fallback and multi-provider strategy
- Evaluate context window and token budget management at scale
- Assess data privacy in training, fine-tuning, and inference pipelines

## Instructions

- Check model version pinning: is the model version fixed or floating? floating versions can silently degrade behavior
- Check prompt versioning: are prompts versioned in code or config? can a regression be detected?
- Check cost controls: is there a token budget per request, per user, or per day? is there an alert?
- Check rate limits: is there retry logic with exponential backoff? is there a circuit breaker?
- Check evaluation: is there a golden dataset or eval suite to detect prompt regressions?
- Check fallback: what happens when the primary model provider is down or rate-limited?
- Check data privacy: is user data sent to external APIs compliant with data agreements?
- Check latency monitoring: is there a p95/p99 tracking for model call latency?

## Heuristics

Treat as stronger concerns when:

- Model version not pinned — behavior can change silently on provider update
- No prompt regression test or golden dataset
- No cost alert or budget cap — unbounded spend risk
- No retry logic on rate limit errors
- No fallback when the primary provider is unavailable
- User PII sent to external LLM APIs without legal/compliance review
- No latency monitoring on user-facing model calls
- Fine-tuning data includes production user data without explicit consent mechanism

Treat as acceptable when:

- No evaluation suite in early prototype or internal tooling stages
- Single provider without fallback in low-criticality internal tools

## Rules

- Model version must be pinned in production — never use `latest` implicitly
- Cost monitoring and budget caps are required before any user-facing rollout
- PII in external model calls requires explicit compliance review
- Prompt changes require evaluation against a known baseline before deployment

## Activity Traceability

🔧 Loading skill: `llm-ops`
