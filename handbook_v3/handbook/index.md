# Handbook

Central knowledge and reasoning framework. Tool-agnostic — readable by any agent via its own entry file.

## Structure

| Directory | Content |
|---|---|
| `roles/` | Reasoning lenses (software-architect, risk-analyst, technical-writer) |
| `skills/` | Reusable capabilities — each as `<name>/SKILL.md` |
| `memory/` | Stable project knowledge |
| `output/` | Ongoing work records, organized by category |
| `commands/` | Named workflows — each as `<name>/COMMAND.md` |
| `workflows/` | Work patterns for different task types |
| `templates/` | Templates for output records |

## Entry files

Each agent reads its own entry file, which references this handbook:

| Agent | Entry file |
|---|---|
| Claude Code | `CLAUDE.md` |
| Codex / OpenAI agents | `AGENTS.md` |
| JetBrains Junie | `.junie/guidelines.md` |
| GitHub Copilot | `.github/copilot-instructions.md` |

## Governance

Output organization, memory management, and work classification rules: `handbook/governance.md`
