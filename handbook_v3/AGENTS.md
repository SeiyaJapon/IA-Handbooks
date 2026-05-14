# Handbook

Working with Francisco Pérez — software architect and engineer at Kintai.

## Structure

| Path | Purpose |
|---|---|
| `handbook/roles/` | Reasoning lenses — load when a different perspective is needed |
| `handbook/skills/` | Reusable capabilities — each in its own folder as `SKILL.md` |
| `handbook/memory/` | Stable project knowledge — load when relevant |
| `handbook/output/` | Ongoing work records — check when continuing previous work |
| `handbook/workflows/` | Named workflows for different types of work |
| `handbook/templates/` | Templates for output records |
| `handbook/governance.md` | Rules for output organization, memory management, and work classification |

## Startup

Read once per session:
- `handbook/memory/project-rules.md`

## Default context

- Role: `handbook/roles/software-architect.md`
- Core skills to consider: `architecture-review`, `code-readability`, `impact-analysis`, `risk-classification`

Load additional roles and skills only when relevant to the task.

## Work classification

Before starting any task, classify it. Use the lightest workflow that fits.

**User signals always override classification.** If the user indicates how they want to work — "let's think this through", "I'll define the approach", "I'm not sure yet" — follow that signal regardless of how the task would otherwise be classified.

When no signal is given:

| Type | Signals | Workflow |
|---|---|---|
| Trivial | Small, clear, reversible, low-risk | Just do it |
| Non-trivial | Multi-part, may continue across sessions, some uncertainty | Use output record — see `handbook/workflows/` |
| Important or ambiguous | Architectural, hard to reverse, unclear path, multiple reasonable options | Pause — ask for collaboration mode |

For important or ambiguous work, ask before acting:

> "This looks important or ambiguous. Do you want me to propose a plan and move forward with my best judgment, would you prefer to define the approach, or should we think it through together first?"

See `handbook/governance.md` for full classification rules and guardrails.

## Output records

When the task is non-trivial or may continue across sessions:

1. List `handbook/output/` to find an existing record
2. If found: read it and continue from `Current State`
3. If not: create one following `handbook/governance.md` using `handbook/templates/work-item.md`

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
