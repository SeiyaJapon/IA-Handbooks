# Handbook Evolution

Ongoing record of deliberate improvements to the handbook structure, based on analysis of external references and accumulated operational learnings.

---

## Current State

Final state after revision (2026-05-04):
- `memory/repos-catalog.md` created
- `memory/project-rules.md` — AI anti-patterns reduced to 4 essential entries (no other home in handbook)
- `handbook/governance.md` created — output organization + memory management rules
- `handbook/commands/` created with 2 commands: self-review, contribute-memory
- `handbook/index.md` updated throughout

No pending items.

---

## History

### 2026-05-04 — Analysis of meta-all / ai-config and first two incorporations

**Context**: Compared the handbook (root of Kintai) with the ai-config bundle from `workspace/meta-all` branch `init-profiles`. ai-config is a full installer + plugin system for a team. The handbook is a reasoning/navigation framework for the agent, simpler, tool-agnostic.

**Conclusions of the comparison**:

The handbook has structural advantages (roles/skills/memory/output separation, tool-agnostic, lightweight, case history via output/). The ai-config has operational density advantages for a team (slash commands, repos catalog, cross-repo MCP search, anti-patterns guardrails, memory discipline, plugins by profile).

What is worth importing, adapted to the handbook's philosophy:
1. ✅ Repos catalog → `memory/repos-catalog.md` (factual, no infrastructure needed)
2. ✅ AI anti-patterns → `memory/project-rules.md` new section (behavioral rules, no dependencies)
3. 🔲 Memory discipline (token limits, curation rules) → destination TBD, to discuss
4. 🔲 Conceptual commands (self-review, contribute-memory, analyze-impact) → destination TBD, to discuss

What was explicitly NOT imported:
- Installer / plugin / profile system (overkill, would break simplicity)
- Team-based profiles (not applicable)
- MCP integrations (infrastructure, not handbook)
- Kintai-specific tech stack rules (AdonisJS idioms, MariaDB, Angular/Metronic)
- PR-driven memory update workflow (over-engineered for individual use)
- Claude Code hooks (PreToolUse) — tool-specific infrastructure
