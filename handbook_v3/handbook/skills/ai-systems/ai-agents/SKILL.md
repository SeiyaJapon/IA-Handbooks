# AI Agents Skill

Design and review **AI agents**: systems where an LLM (or other model) maintains state, plans, calls tools, and selects actions autonomously or semi-autonomously. This skill is for AI/LLM agents, not for software design "agents" (objects, processes, roles in a non-AI system).

## When to use

Use this skill when:

- The system has **autonomous or semi-autonomous control flow** driven by an LLM.
- The system uses **tool calls** (function calling, MCP servers, APIs) chosen by the model at runtime.
- The system maintains **memory** across turns or sessions and uses it in subsequent reasoning.
- The system uses a **planning loop** (ReAct, plan-then-execute, tree-of-thought, etc.).
- The system is **multi-agent**: several LLMs coordinate, hand off, or specialise.
- The user asks "should we build an agent?", "is this an agent?", or "review this agent".

## When not to use

Do not use this skill when:

- The system is a **single LLM call** with no tools, no memory, no loop. Use `ai-integration`.
- The system is a **deterministic workflow** with one or more LLM calls inside (a step function, a queue consumer, a scripted pipeline). Use `ai-integration` and the relevant orchestration skill (`event-driven-architecture`, `data-pipeline-architecture`).
- The "agent" is a **software design abstraction** (an object, a worker process, a domain role) that does not use AI. Use the relevant `software-design` or `software-architecture` skill.
- The work is **prompt iteration without autonomy**. Use `prompt-engineering`.
- The work is **retrieval-augmented generation** without planning or tool selection. Use `rag-design`.
- The work is **operating LLM systems** (eval, drift, cost dashboards, model rotation). Use `llm-ops`.

A single prompt + LLM call is not an agent. A workflow with deterministic steps and one or more LLM calls is not automatically an agent. Use this skill only when the system has autonomous or semi-autonomous control flow, tool use, planning, memory, or action selection.

## Inputs to inspect first

Before reviewing or designing an agent, inspect:

- **The agent's task.** What is it expected to accomplish? In one sentence, in user terms.
- **The autonomy boundary.** What can the agent do without human approval? What requires approval? What is forbidden?
- **The tool surface.** Which tools? What does each do? What is the worst thing each could do if misused?
- **Memory shape.** Short-term context window, conversation history, long-term store, episodic. What is retained, where, for how long.
- **The control loop.** ReAct, plan-then-execute, tool-calling loop. Termination conditions. Step and cost limits.
- **Permissions and identity.** What credentials does the agent run under? What can those credentials touch?
- **Observability.** Are tool calls, prompts, completions, and decisions logged for post-hoc review?
- **Eval and rollback.** How is quality measured? How is a bad version rolled back?

If any of these is unknown, agent-level judgements are guessing.

## Hard rules

- **No infinite loops.** Every agent loop has a hard step limit and a hard cost limit. Reaching either terminates the run with a clear failure.
- **No silent irreversible actions.** Actions that cannot be undone (sending emails, charging money, deleting data, posting publicly, modifying production) require human approval, reversibility, or an explicit policy that documents the risk.
- **Least-privilege tools.** A tool's permissions are the narrowest that lets it work. `run_any_sql`, `execute_any_command`, broad write access are smells unless explicitly scoped.
- **Tool call results are validated.** The agent does not blindly trust tool outputs; the loop validates structure, sanity, and authorisation context where relevant.
- **Prompt injection is treated as a real threat.** Inputs from users, retrieved documents, tool outputs, and external content can carry adversarial instructions. The agent's prompt structure separates trusted instructions from untrusted content; outputs are validated.
- **Every action is traceable.** Tool calls, prompts, completions, decisions are logged. A post-hoc review can reconstruct what happened.
- **Failures are loud, not silent.** A tool call that fails is logged and surfaced; the agent does not hallucinate a successful result.
- **Cost is monitored.** Per-run, per-user, per-day, per-feature.
- **Safety and blast radius are scoped explicitly.** What is the worst the agent can do per run? Does the team know? Is there a kill switch?
- **A manual override exists.** Operators can stop a run, roll back a model/prompt version, or disable the agent without redeploy.

## Concerns this skill covers

### Tool use

- Each tool has a clear input/output contract.
- Tool descriptions are precise (the LLM uses them to choose).
- Tool permissions are the minimum required.
- Tool failures are caught, surfaced, and turned into recoverable signals.
- Tool outputs are not blindly trusted (sanitisation, validation, sandboxing).

### Memory

- Short-term: context window. Pruning and summarisation strategies when limits are hit.
- Long-term: persisted memory (vector store, database). Retrieval criteria, freshness, eviction.
- Per-session vs cross-session: explicit choice, with privacy and security implications.
- Memory growing without bound is a smell.

### Planning loops

- ReAct, plan-then-execute, tree-of-thought, scripted plans.
- Termination: step limit, cost limit, success criterion, no-progress detection.
- Re-planning when partial steps fail.
- Avoiding infinite loops on contradictory tools or unclear goals.

### Autonomy boundaries and permissions

- Read vs write actions.
- Reversible vs one-way actions.
- Confined-environment actions (sandbox, dry-run) vs production actions.
- Permission grants per tool, per session, per user.

### Human approval

- When required: irreversible actions, high-cost actions, actions affecting many users, actions in regulated domains.
- How to ask: surfaced in the UX, with enough context for the human to decide.
- Defaults: default-deny for high-risk; default-allow only for documented low-risk paths.

### Stopping conditions

- Step limit reached.
- Cost limit reached.
- Success criterion met.
- Failure mode detected (tool errors exceeding threshold, contradictory plan, hallucinated tool calls).
- Human stop signal.

### Tool failure handling

- Distinguish transient (retry with backoff) from permanent (escalate, surface).
- Distinguish failure-of-tool from failure-of-input.
- Avoid the agent fabricating a "successful" outcome.

### Prompt injection and tool injection

- Threat model: untrusted user input, retrieved documents, tool outputs, third-party content.
- Mitigations: structural separation between instructions and untrusted content, allowlists for tools, output validation, second-pass review of suspicious patterns.
- Special caution with tools that consume the agent's own past outputs.

### Observability

- Every prompt, completion, tool call, decision logged with correlation IDs.
- Cost per run, latency per step, failure modes per tool.
- Aggregated dashboards for operations.

### Evaluations

- Pre-deployment: golden tasks the agent must pass; regression suite.
- In production: sampled traces graded by humans or LLMs-as-judge.
- Per-tool evaluation: does each tool behave as the agent expects?

### Cost control

- Per-call, per-run, per-user, per-day caps.
- Cheaper models for filtering, frontier models for hard steps.
- Caching where the same input recurs.

### Safety, blast radius, rollback

- Worst-case per run: what is the maximum impact if the agent goes wrong?
- Containment: sandboxing, dry-run, staged execution, transactionality.
- Rollback: ability to revert model version, prompt version, tool set without redeploy.
- Manual override: kill switch, pause-the-fleet capability.

### When not to build an agent

- A workflow with deterministic steps fits better.
- A single LLM call with output schema fits better.
- A queue consumer with idempotent handlers fits better.
- A search + template fits better.
- The autonomy adds risk without value.

The default answer to "should we build an agent?" is often "no, build a workflow with one or more LLM calls". Use agents when the variability of the task genuinely demands runtime decision making.

## How to work

1. **Confirm it is an agent.** Apply "When to use" / "When not to use". If single call or deterministic workflow, route elsewhere.
2. **Map the task and autonomy boundary.** What does it do? What can it do without approval?
3. **Inspect tools.** Permissions, contracts, failure modes. Each tool by its worst-case use.
4. **Inspect memory.** Short-term and long-term; pruning, eviction, privacy.
5. **Inspect the loop.** Termination conditions, step and cost limits, re-planning.
6. **Inspect prompt structure.** Trusted instructions vs untrusted content; injection mitigations.
7. **Inspect observability.** Logs, traces, dashboards.
8. **Inspect eval and rollback.** Quality measurement and operational override.
9. **Recommend the smallest change** that closes the active risk. Often: a tighter tool permission, a step limit, a human approval gate, a logging field.
10. **If the agent is overkill**, recommend a workflow or a single-call design instead.

## Output

Return findings as:

- **Concrete violations:** unbounded loops, broad tool permissions, irreversible actions without approval, missing observability, prompt injection exposure, no rollback path.
- **Risk classification:** for each finding, the worst-case impact and likelihood.
- **Recommended changes:** tightest permission, narrowest tool, smallest approval gate.
- **Architecture escalations:** when the right answer is a workflow or a single call, not an agent.
- **Refusal to apply:** when a "concern" is theoretical and the agent's blast radius is genuinely small.

## Escalation

- If the question is **about prompt design**, including the agent's system prompt: load `prompt-engineering`.
- If the question is **about retrieval** (the agent uses RAG as a tool): load `rag-design`.
- If the question is **about the LLM API integration** (clients, retries, streaming): load `ai-integration`.
- If the question is **about model versions, evaluation harnesses, drift detection, cost dashboards in production**: load `llm-ops`.
- If the question is about whether **AI is the right tool at all**: load `ai-systems` (the mega-skill).
- If the agent's workflow could be **deterministic** (a step function, a workflow engine): load `event-driven-architecture` or `data-pipeline-architecture` and reconsider whether an agent is needed.
- If the agent's actions are **on infrastructure** (databases, deployments): load `security-review` and the relevant infrastructure skill; agents touching production need extra scrutiny.

## What this skill does NOT do

- Replace deterministic engineering. Most "agent" problems are workflow problems.
- Replace prompt engineering. Prompt design is a separate, deep concern.
- Replace LLM ops. Operating an agent in production needs the eval, drift, cost, and rollback machinery covered by `llm-ops`.
- Validate that the agent should exist. That is the user's call; this skill informs.
