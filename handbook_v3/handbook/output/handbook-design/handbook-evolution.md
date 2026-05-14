# Handbook Evolution

Ongoing record of deliberate improvements to the handbook structure, based on analysis of external references and accumulated operational learnings.

---

## Current State

Final state after revision (2026-05-10):

**Multi-agent architecture — v2**
- `CLAUDE.md` rewritten as short entry file for Claude Code (references handbook directly)
- `AGENTS.md` rewritten as short entry file for Codex / OpenAI agents (same structure)
- `.junie/guidelines.md` created — entry for JetBrains Junie
- `.github/copilot-instructions.md` created — entry for GitHub Copilot (code-focused)
- `.claude/commands/self-review.md` and `contribute-memory.md` created — slash commands for Claude Code
- `handbook/skills/` — all 13 skills converted from flat `.md` to folder + `SKILL.md` format
- `handbook/commands/` — `index.md` → `COMMAND.md` in both commands
- `handbook/roles/default.md` removed — default role now declared in each entry file
- `handbook/skills/default.md` removed — default skills now declared in each entry file
- `handbook/index.md` simplified — now describes structure and entry points only (no startup chain)

No pending items.

---

## History

### 2026-05-10 — Multi-agent architecture v2

**Context**: CTO feedback that the previous setup was "how things were done 6 months ago". Discussion revealed the feedback was Claude-only focused (SKILL.md naming, COMMAND.md naming, skills with zips/scripts). The handbook is intentionally multi-agent and tool-agnostic — this is a feature, not a limitation.

**Decisions**:
- Kept `handbook/roles/` and `handbook/skills/` separate — the distinction between reasoning lenses and reusable capabilities is semantically valid and useful
- No symlinks — direct path references in each entry file are simpler and equally effective
- No MCP coupling — lightweight is intentional for portability
- GitHub Copilot entry is code-focused by design — Copilot does not navigate files like a reasoning agent
- `.claude/commands/` commands reference the handbook definitions rather than duplicating content

**Token impact**: Startup reduced from 5+ mandatory file reads to 1 (`project-rules.md`). Skills and roles load on demand.

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
