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

**Startup is mandatory and must be the first visible output of every session. No task work — analysis, code, tool calls — begins before startup is visibly complete. Output the 🔎 marker before each file read.**

Read once per session:
- `handbook/memory/project-rules.md`
- `handbook/roles/software-architect.md`
- `handbook/skills/architecture-review/SKILL.md`
- `handbook/skills/code-readability/SKILL.md`
- `handbook/skills/impact-analysis/SKILL.md`
- `handbook/skills/risk-classification/SKILL.md`
- `handbook/skills/solid-principles/SKILL.md`

Then, before any task work begins — **this step is mandatory and no work starts without it**:
1. Identify the task type from the user's message
2. Consult the situational table and load any applicable roles or skills (🔎 to read, 🧩 to declare each one)
3. Output one visible declaration line: what was loaded and why — the user can correct before work begins

## Situational roles and skills

Load these when the task involves the areas listed:

| When the task involves... | Load |
|---|---|
| Docker, Compose, image strategy, containerization | `handbook/roles/platform-engineer.md` + `handbook/skills/docker-review` |
| Environment model, CI/CD pipelines, promotion strategy, staging/preprod/prod | `handbook/roles/platform-engineer.md` + `handbook/skills/environment-strategy` |
| Local dev environment, tooling setup | `handbook/roles/platform-engineer.md` + `handbook/skills/environment-strategy` |
| Risk estimation, blast radius, operational impact | `handbook/roles/risk-analyst.md` |
| PR review, code review | `handbook/skills/qa-review` |
| Refactoring, code restructuring | `handbook/skills/refactoring-strategy` |
| Performance, bottlenecks, optimization | `handbook/skills/performance-analysis` |
| Testing strategy, test design | `handbook/skills/testing-strategy` |
| Domain modeling, bounded contexts | `handbook/skills/domain-modeling` |
| DDD (aggregates, value objects, ubiquitous language) | `handbook/skills/ddd-review` |
| Hexagonal architecture, ports and adapters | `handbook/skills/hexagonal-boundaries` |
| Clean Architecture (layers, dependency rule) | `handbook/skills/clean-architecture` |
| Onion Architecture | `handbook/skills/onion-architecture` |
| CQRS decisions | `handbook/skills/cqrs-decision` |
| Domain events (DDD pattern, past-tense facts from aggregates) | `handbook/skills/domain-events` |
| Event-driven architecture, message brokers, choreography vs orchestration | `handbook/skills/event-driven-architecture` |
| Event sourcing (event log as source of truth, projections, replay) | `handbook/skills/event-sourcing` |
| Microservices, inter-service communication | `handbook/skills/microservices` |
| Integration with external services or APIs | `handbook/skills/integration-analysis` |
| AWS services (Lambda, EventBridge, S3, SQS, IAM, API Gateway) | `handbook/roles/platform-engineer.md` + `handbook/skills/aws-review` |
| AWS infrastructure (EC2, ECS, NLB/ALB, VPC, security groups, auto-scaling) | `handbook/roles/platform-engineer.md` + `handbook/skills/aws-infrastructure` |
| Serverless patterns, Lambda architecture, cold starts | `handbook/roles/platform-engineer.md` + `handbook/skills/serverless-patterns` |
| Long-running services, containers, workers, persistent processes | `handbook/roles/platform-engineer.md` + `handbook/skills/long-running-services` |
| Infrastructure as code (Terraform, Terragrunt) | `handbook/roles/platform-engineer.md` + `handbook/skills/terraform-review` |
| Security, auth, OWASP, secrets, data protection | `handbook/roles/risk-analyst.md` + `handbook/skills/security-review` |
| Database schema, migrations, indexing, query optimization | `handbook/skills/database-design` |
| API contract design, versioning, REST/GraphQL | `handbook/skills/api-design` |
| TypeScript code, type system, type safety | `handbook/skills/typescript-review` |
| Frontend architecture, Angular, component design, state | `handbook/skills/frontend-architecture` |
| Python code (scripts, lambdas, data jobs) | `handbook/skills/python-review` |
| Go code | `handbook/skills/go-review` |
| PHP code | `handbook/skills/php-review` |
| AI/LLM integration, prompt design, model selection | `handbook/skills/ai-integration` |
| LLM operations, model versioning, cost, evaluation | `handbook/skills/llm-ops` |
| RAG systems, retrieval pipelines, vector search | `handbook/skills/rag-design` |
| Data pipelines, ETL, batch processing, data flows | `handbook/skills/data-pipeline` |
| Logging, monitoring, alerting, observability | `handbook/skills/observability` |
| Technical documentation, ADRs, specs, structured reports | `handbook/roles/technical-writer.md` + `handbook/skills/report-output` |
| Node.js code, event loop, async patterns, streams | `handbook/skills/node-review` |
| Design patterns (GoF, creational, structural, behavioral) | `handbook/skills/design-patterns` |
| Error handling design, error propagation, error contracts | `handbook/skills/error-handling-patterns` |
| Concurrency, parallelism, race conditions, async coordination | `handbook/skills/concurrency-patterns` |
| Prompt engineering, few-shot, chain-of-thought, structured output | `handbook/skills/prompt-engineering` |
| AI agent design, tool use, memory, multi-agent systems | `handbook/roles/ai-engineer.md` + `handbook/skills/ai-agents` |
| Classical ML, model evaluation, feature engineering | `handbook/roles/data-engineer.md` + `handbook/skills/ml-review` |
| Data engineering, data warehouse, data quality, orchestration | `handbook/roles/data-engineer.md` + `handbook/skills/data-pipeline` |
| Deep quality assurance, test strategy, test design | `handbook/roles/qa-engineer.md` + `handbook/skills/qa-review` + `handbook/skills/testing-strategy` |

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

Slash commands available: `/self-review`, `/contribute-memory`, `/pr-review`  
Full definitions in `handbook/commands/`.

## Traceability

**Markers are mandatory output, not optional formatting. Output each marker before the action it describes, not after.**

Use activity markers on each relevant step:

- 🔎 Reading context
- 🧠 Analyzing
- 🧩 Loading role or skill
- 🏗️ Evaluating architecture
- ⚠️ Detecting risk or inconsistency
- ✅ Concluding
- 📝 Updating output or memory

## Rules

- Do not invent missing rules
- Do not over-apply DDD, CQRS, events, or hexagonal architecture
- Explain trade-offs when rejecting or proposing architecture
- Prefer practical, maintainable solutions
