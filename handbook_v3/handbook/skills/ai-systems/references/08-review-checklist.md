# 08. Review Checklist

## Step 1: Is AI the right tool?

1. Was a non-AI alternative considered?
2. Does the problem genuinely fit AI's strengths?
3. Are accuracy, latency, cost budgets compatible?

## Step 2: Discipline applied

1. Which AI sub-disciplines are in use (integration, agent, RAG, prompt, ops, ML)?
2. Are the right sub-skills loaded for design and review?

## Step 3: Evaluation

1. Is there a golden dataset or evaluation harness?
2. Is regression detected automatically?
3. Are quality metrics monitored in production?

## Step 4: Cost and latency

1. Cost budget defined and monitored?
2. Latency budget defined and monitored?
3. Smallest model that meets quality?
4. Caching, batching, streaming where applicable?

## Step 5: Safety

1. Hallucination mitigations?
2. Prompt injection mitigations?
3. Data privacy compliant?
4. Output validation?

## Step 6: Vendor and model

1. Vendor and model choice justified?
2. Vendor coupling isolated (port + adapter)?
3. Model version pinned?
4. Rotation plan in place?

## Step 7: Failure handling

1. Timeouts, retries on transient failures?
2. Fallback when AI is unavailable or low confidence?
3. User-facing experience when AI fails?

## Step 8: Discipline-specific (run the relevant sub-skill checklists)

- For agents: tool design, memory, termination, cost limits.
- For RAG: retrieval evaluation, chunking, context window.
- For prompts: versioning, evaluation, structured output.
- For LLM ops: cost dashboards, drift detection, model rotation.
- For classical ML: training/serving skew, retraining triggers.

## Summary

Top three findings. Quick wins. Backlog. Confirmed strengths.

## Forbidden conclusions

- "It is fine because we use the latest model."
- "It is fine because users are happy."
- "It is fine because the prompt looks good."

The properties are problem fit, evaluation, cost, latency, safety, vendor management, fallback. Model name and informal feedback alone do not validate the system.
