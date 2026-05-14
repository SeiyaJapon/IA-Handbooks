# 🚀 Handbooks for AI Agents 🤖✨

Welcome to the **most complete and awesome** collection of handbooks to master AI-assisted development!

This repository is designed so you can get the most out of AI tools like Claude, Copilot, Cursor, Grok, Junie, and more. Get ready to revolutionize your workflow! 🔥

## 🌟 Why this project is amazing for AI topics?

- **Smart structures**: Roles, reusable skills, persistent memory, and clear governance for AI agents.
- **Multi-tool**: Handbooks adapted to different editors and coding assistants (Claude, Cursor, Copilot, Grok, Junie).
- **Brutal productivity**: Reduces onboarding time and improves code quality with AI.
- **Designed by experts**: Based on real experiences from software architects.
- **Totally extensible!** Easily add your own roles and skills.

## 📂 Repository Structure

| Directory               | Description                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| `handbook_v1/`          | First version of the base handbook                                          |
| `handbook_v2/`          | Extended version with multi-tool support (Claude, Copilot, Grok, Junie, MCP) |
| `handbook_v3/`          | Latest version: full handbook with skills, roles, workflows, and memory     |
| `handbook_as_editor/`   | Specific focus for use as an AI editor                                      |

### handbook_v2

Extends v1 with integrations for multiple AI tools:

- `AGENTS.md`, `CLAUDE.md`, `GROK.md` — ready-to-use instructions per tool
- `.claude/commands/` — slash commands for Claude (contribute-memory, pr-review, self-review)
- `.claude/settings.local.json` — Claude permissions and configuration
- `.github/copilot-instructions.md` — GitHub Copilot project instructions
- `.junie/guidelines.md` + `.junie/memory/` — Junie AI assistant integration
- `.ai/mcp/mcp.json` — MCP server configuration
- `handbook/` — core content: roles, skills, memory, commands, and output

### handbook_v3

Most complete version. Includes everything in v2 plus a rich `handbook/` structure:

| Section                       | Contents                                                                                                         |
|-------------------------------|------------------------------------------------------------------------------------------------------------------|
| `handbook/skills/`            | 14 reusable skills: architecture-review, code-readability, cqrs-decision, domain-events, domain-modeling, hexagonal-boundaries, impact-analysis, integration-analysis, performance-analysis, qa-review, refactoring-strategy, report-output, risk-classification, testing-strategy |
| `handbook/roles/`             | 3 agent roles: software-architect, risk-analyst, technical-writer                                                |
| `handbook/workflows/`         | 4 workflows: planned-change, lightweight-change, investigation, collaborative-planning                           |
| `handbook/commands/`          | Commands with detailed COMMAND.md: contribute-memory, pr-review, self-review                                     |
| `handbook/memory/`            | Persistent context: architecture-decisions, glossary, integration-context, project-rules, repos-catalog          |
| `handbook/output/`            | Structured output tracking: audits, handbook-design, proposals, tasks                                           |
| `handbook/governance.md`      | Governance model for AI agent usage                                                                              |
| `handbook/templates/`         | Reusable templates (e.g. work-item)                                                                              |

Also includes the same multi-tool files as v2: `AGENTS.md`, `CLAUDE.md`, `GROK.md`, `copilot-instructions.md`, `junie_guidelines.md`, `.claude/`, `.junie/`, `.ai/mcp/`.

## 🛠️ Getting Started

1. Clone the repo:
   ```bash
   git clone <your-repo>
   cd handbooks
   ```

2. Choose your preferred version (we recommend `handbook_v3` for the latest and most powerful).

3. Load the context in your favorite AI tool and start creating magic! ✨

## 🤝 Contributing

Do you have ideas to improve the handbooks or new AI skills? Pull requests welcome!

## 📜 License

Free for personal and professional use. May the AI be with you! 🌌

---

Made with ❤️ and lots of ☕ for the AI community.