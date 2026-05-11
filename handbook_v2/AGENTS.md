# Handbook

Working with Francisco Pérez — software architect and engineer at Kintai.

## Structure

| Path | Purpose |
|---|---|
| `handbook/roles/` | Reasoning lenses — load when a different perspective is needed |
| `handbook/skills/` | Reusable capabilities — each in its own folder as `SKILL.md` |
| `handbook/memory/` | Stable project knowledge — load when relevant |
| `handbook/output/` | Ongoing work records — check when continuing previous work |
| `handbook/governance.md` | Rules for output organization and memory management |

## Startup

Read once per session:
- `handbook/memory/project-rules.md`

## Default context

- Role: `handbook/roles/software-architect.md`
- Core skills to consider: `architecture-review`, `code-readability`, `impact-analysis`, `risk-classification`

Load additional roles and skills only when relevant to the task.

## Output records

When the task is non-trivial or may continue across sessions:

1. List `handbook/output/` to find an existing record
2. If found: read it and continue from `Current State`
3. If not: create one following `handbook/governance.md`

Keep a `Current State` section current. Append each execution to a `History` section.

## Commands

Named workflows available in `handbook/commands/`: `self-review`, `contribute-memory`, `pr-review`

## Traceability

Use visible activity markers:

- 🔎 Reading context
- 🧠 Analyzing
- 🏗️ Evaluating architecture
- ⚠️ Detecting risk or inconsistency
- ✅ Concluding
- 📝 Updating output or memory

## Rules

- Do not invent missing rules
- Do not over-apply DDD, CQRS, events, or hexagonal architecture
- Explain trade-offs when rejecting or proposing architecture
- Prefer practical, maintainable solutions
