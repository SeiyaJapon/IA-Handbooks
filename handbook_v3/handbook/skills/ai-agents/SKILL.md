# AI Agents

## Purpose

Review the design of AI agent systems: tool use, memory, planning, multi-agent coordination, and operational safety.

## Responsibilities

- Evaluate agent architecture: single agent vs multi-agent, orchestration vs choreography
- Review tool design and tool call safety
- Assess agent memory design: short-term, long-term, episodic
- Evaluate planning and reasoning loop design
- Review human-in-the-loop and approval mechanisms
- Assess error handling and recovery in agentic loops
- Evaluate agent observability and audit trail
- Review blast radius and permission scope of agent actions

## Instructions

- Identify the agent type: reactive, goal-directed, or planning-based
- Check tool definitions: is each tool narrowly scoped? does it have clear input/output contracts?
- Check tool safety: can the agent take irreversible actions without human confirmation?
- Check memory: is context accumulated across turns? is there a pruning or summarization strategy?
- Check the reasoning loop: is there a maximum iteration limit? can it loop forever?
- Check multi-agent communication: how do agents pass state? is there a shared context or message passing?
- Check error recovery: what happens when a tool call fails? does the agent retry, escalate, or give up?
- Check observability: is there a trace of every tool call, decision, and output for post-hoc review?

## Heuristics

Treat as stronger concerns when:

- Agent can take irreversible actions (send emails, delete data, charge money) without confirmation
- No maximum iteration or tool call limit — infinite loop risk
- Tool with overly broad permissions (`run_any_sql`, `execute_any_command`)
- No audit trail for agent actions — cannot reconstruct what happened
- Multi-agent system with shared mutable state and no coordination protocol
- Agent memory that grows unboundedly per session
- No fallback when a tool call fails — agent hallucinates a result and continues
- Agent prompt that can be hijacked by content retrieved from the environment (indirect prompt injection)

Treat as acceptable when:

- No human-in-the-loop for low-risk read-only operations
- Simple reactive agent without planning loop for narrow, well-defined tasks

## Rules

- Irreversible actions require explicit human confirmation or a reversibility mechanism
- Every agent loop must have a maximum iteration limit
- Tool permissions must be scoped to the minimum required
- Every agent action must be traceable after the fact

## Activity Traceability

🔧 Loading skill: `ai-agents`
