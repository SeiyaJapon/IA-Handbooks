# LLM Ops Skill

Operate LLM-based systems in production: **evaluation, observability, regression testing, cost monitoring, latency, model and version changes, rollout, rollback**. The day-2 concerns of any AI system that left the prototype.

## When to use

Use this skill when:

- An AI system is **going to production** or is already there.
- A model or prompt **rotation** is being planned and the team needs to verify quality before/after.
- Cost or latency is **regressing** and the team needs dashboards and budgets.
- The system is **drifting** and the team needs detection.
- A **rollback** is needed (bad model version, bad prompt) and the team needs the operational machinery.

## When not to use

Do not use this skill when:

- The system is **a prototype** with no users and no SLA. LLM-ops overhead is not paid back yet (but define eval criteria early; that part stays).
- The work is **prompt design**. Use `prompt-engineering`. Llm-ops verifies the prompt in production; the design itself is upstream.
- The work is **model integration** (clients, retries, streaming). Use `ai-integration`.
- The work is **retrieval evaluation** specifically. Use `rag-design` for retrieval-side eval; `llm-ops` covers generation-side and end-to-end eval.
- The work is **incident response**. That is operational; this skill prepares for it but does not run it.

## Inputs to inspect first

Before recommending llm-ops changes, inspect:

- **Production metrics.** Cost per call, latency p50/p95/p99, error rate, model version distribution.
- **Eval harness.** Golden dataset, LLM-as-judge config, human eval cadence.
- **Rollout strategy.** Canary, percentage, A/B, shadow.
- **Rollback path.** How is a bad model version or prompt reverted? In how long?
- **Versioning.** Are model versions pinned? Are prompts versioned in code?
- **Drift signals.** Input distribution, output distribution, quality samples over time.
- **Cost guardrails.** Per-call, per-user, per-day caps; alerts on spikes.
- **Incident readiness.** Runbook for "model down", "prompt regressed", "cost spiked".

## Hard rules

- **Pin model versions in production.** Floating versions degrade silently on vendor updates.
- **Eval harness exists before any user-facing deployment.** Golden dataset, regression detection, baseline.
- **Cost guardrails before any user-facing rollout.** Per-call, per-user, per-day caps, alerts.
- **Latency monitored.** p50/p95/p99 per call and end-to-end. Streaming where users wait.
- **Rollback path tested.** "Easy to roll back" without verification is a future incident.
- **Drift detection.** Input distribution, output quality, model-version side effects.
- **Privacy verified.** Data sent to vendor APIs reviewed against compliance.
- **Sampled traces in production.** A subset of calls retained for human or LLM-as-judge review.

## Concerns this skill covers

- **Eval harness:** golden dataset, LLM-as-judge, A/B in production.
- **Regression testing:** prompt changes verified against baseline before deploy.
- **Cost dashboards and alerts:** per-call, per-feature, per-user, per-day.
- **Latency dashboards:** p50/p95/p99 per call, end-to-end per feature.
- **Model rotation:** strategy when vendors deprecate, when newer models offer cost/quality wins, when incidents force rotation.
- **Prompt rotation:** versioned prompts; deploy with rollback ready.
- **Drift detection:** input distribution shifts, output quality drift, vendor side effects.
- **Multi-provider strategy:** fallback when primary provider is down or rate-limited.
- **Sampled traces:** which calls are retained for review, with what privacy.
- **Privacy and compliance:** data classification, vendor agreements, regulated data handling.
- **Incident readiness:** runbooks, rollback drills, kill switches.

## How to work

1. **Inspect production metrics** to identify the active concern (cost, latency, quality, drift).
2. **Check eval harness presence and freshness.** Without one, the team is blind.
3. **Check version pinning** for model and prompts.
4. **Check rollout and rollback** machinery.
5. **Check guardrails:** cost, latency, rate limits, privacy.
6. **Check drift signals.**
7. **Recommend the smallest change** that closes the active gap.

## Output

Return findings as:

- Concrete operational gaps with named metric or workflow.
- Recommended changes (eval harness, cost dashboard, rollback drill, version pin).
- Escalations when the underlying issue is in design (prompts, integration, retrieval, agent) rather than operations.

## Escalation

- The prompts being operated: `prompt-engineering`.
- The integration being operated: `ai-integration`.
- Retrieval performance and drift: `rag-design`.
- Agent operations specifically: `ai-agents` (this skill complements, focuses on the agent's autonomy machinery).
- Whether AI is the right tool at all: `ai-systems`.
- Production observability that crosses AI/non-AI boundaries: `observability`.
- Privacy or compliance of model calls and stored data: `security-review`, `compliance-patterns`.
- Cost in the broader cloud sense: `aws-infrastructure` or relevant vendor skill.

## Operational checks (legacy, kept as a checklist)

- Model version not pinned; behaviour can change silently on provider update.
- No prompt regression test or golden dataset.
- No cost alert or budget cap; unbounded spend risk.
- No retry logic on rate limit errors.
- No fallback when the primary provider is unavailable.
- User PII sent to external LLM APIs without legal/compliance review.
- No latency monitoring on user-facing model calls.
- Fine-tuning data includes production user data without explicit consent.
