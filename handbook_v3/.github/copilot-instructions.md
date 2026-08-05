# Copilot Instructions

Working with Francisco Pérez — software architect and engineer at Kintai.

## Routing phase (mandatory)

Before any task work, run the routing phase as defined in `handbook/governance.md` ("Routing as a mandatory phase"):

1. Read the request literally and intentionally.
2. Scan `handbook/situational-table.md` (the catalogue of skills and roles). Apply the "When to consult" filter at the top of that file. Use `handbook/routing-guide.md` for principles when the row is ambiguous. Classify active concerns; identify the primary; identify supporting concerns that materially affect the answer.
3. Load the chosen skills.
4. Process the problem.

The routing decision is visible in the response, before task work, in the `🎭 / 🧩 / ♻️` or `🔄` block (see Context declaration below).

## Architecture default

- Default: hexagonal architecture with DDD for backend services
- Layers: domain → application → infrastructure (dependencies point inward only)
- Domain code must not depend on frameworks, ORMs, HTTP, or databases
- Business logic belongs in domain or application layer, never in controllers or handlers

## Code quality

- Name variables, functions, and constants by what they mean, not by what they do mechanically
- Never abbreviate (`c`, `tmp`, `data`, `res` are not acceptable names)
- A function is named by its intent, not its implementation
- An orchestrating function reads like prose
- Structural repetition is always a signal of missing abstraction

## Patterns

- Repository pattern for data access
- Use cases as application layer entry points
- Domain events for cross-context communication
- Value objects for domain concepts with validation rules

## Context declaration

**Mandatory at the start of every response, without exception. The full block (`🎭 → 🧩 → ♻️/🔄`) must be the first visible output before any task content. Omitting it or merging it with other content means the response has not started correctly.**

When context is unchanged from the previous response:

```
🎭 Rol activo: <role name>
🧩 Skills cargadas: <skill-a> + <skill-b>
♻️ Contexto sin cambios respecto al paso anterior
```

When context changes (role or skills differ from previous response):

```
🎭 Rol activo: <role name>
🧩 Skills cargadas: <skill-a> + <skill-b>
🔄 Contexto actualizado — <brief reason>
```

Rules:
- Role on the first line, skills on the second — never compact into one line
- Third line is always present: `♻️` if unchanged, `🔄` with reason if changed
- Applies to every response, including trivial ones

## Commands

Named workflows available in `handbook/commands/`: `self-review`, `contribute-memory`, `pr-review`

## Project knowledge

See `handbook/memory/project-rules.md` for project-specific conventions and gotchas.
See `handbook/roles/software-architect.md` for architectural standards applied in this project.
See `handbook/roles/platform-engineer.md` when the task involves Docker, CI/CD, or environment strategy.
See `handbook/skills/docker-review` when reviewing Dockerfiles or Compose files.
See `handbook/skills/environment-strategy` when evaluating deployment pipelines or environment models.
See `handbook/roles/technical-writer.md` + `handbook/skills/prose-report` when writing reports for mixed audiences (engineering + product) in prose-first style.
See `handbook/skills/software-design/cli-design` when designing command-line interfaces, subcommands, exit codes, or `--help` output.
See `handbook/skills/software-design/subprocess-management` when launching, supervising, or cleaning up child processes.
See `handbook/skills/software-design/dependency-injection` when wiring dependencies, designing composition roots, or defining ports/Protocols.
See `handbook/skills/software-design/ddd` for any DDD-related work (subdomains, bounded contexts, aggregates, value objects, ubiquitous language, domain events).
See `handbook/skills/software-architecture/hexagonal-architecture` for ports and adapters.
See `handbook/skills/financial-domain-patterns` when working with money, invoices, currencies, tax rates, or accounting entries.
See `handbook/roles/risk-analyst.md` + `handbook/skills/compliance-patterns` when handling audit trails, data retention, or payment data.
See `handbook/skills/status-presentation` when answering panoramic status / agenda questions ("where are we", "what's pending").

For the full situational table (all architectures, design sub-skills, AI sub-skills, language reviews), see `handbook/situational-table.md`.
