# AGENTS.md

At startup, read `handbook/index.md` and announce the read:

🔎 Reading context: `handbook/index.md`

Then follow `handbook/index.md`.

## Traceability

1. Only create a work record when the case is expected to continue or has long-term value
2. Use a descriptive case name, not the branch name
3. When the case is non-trivial or may continue, list handbook/output/ to discover existing records
4. If an existing record matches the case, read it and continue from its latest state
5. If no record exists, create one only when the case is expected to continue or has long-term value
6. Keep the latest state in a “Current State” section
7. Append each new execution to a “History” section
8. Do not delete previous iterations
9. If the case produces a stable architectural decision, suggest moving it to handbook/memory/

Before creating any record in handbook/output/, read and apply handbook/governance.md. Output records must never be placed directly in handbook/output/ — a category subfolder is mandatory.

## Continue Flow

1. Continue from the matching output record when available
2. Continue from handbook/memory/ when the knowledge is stable
3. Do not restart from zero if context already exists
4. Do not assume undocumented context
5. If several records could match, ask which one to continue

## Activity Traceability

Use visible activity markers while working.

Each relevant line must show what is happening:

- 🔎 Reading context
- 🧠 Analyzing
- 🧩 Applying role or skill
- 🏗️ Evaluating architecture
- ⚠️ Detecting risk or inconsistency
- ✅ Concluding
- 📝 Updating output or memory

Do not expose hidden chain-of-thought.

Show only useful operational traceability.

## Emoji Reference

- 🔎 Context lookup
- 🧠 Reasoning
- 🧩 Role / skill usage
- 🏗️ Architecture
- 🧱 Domain model
- 🔌 Integration / adapters
- ⚠️ Risk
- ❌ Rejected option
- ✅ Decision
- 📝 Record update
- 📌 Memory candidate
- 🧭 Routing decision

## Rules

For startup, routing, and handbook structure: follow `handbook/index.md`.
For output organization and memory management: follow `handbook/governance.md`.
For architectural and project behavioral rules: follow `handbook/memory/project-rules.md`.

Rules with no other home:

- Do not invent missing rules
- Do not over-apply DDD, CQRS, events, or hexagonal architecture
- Explain trade-offs when rejecting or proposing architecture
- Prefer practical, maintainable solutions

## Example — Full Session

User asks:

“Review this new use case and tell me if the domain event makes sense.”

Expected flow:

🔎 Check handbook/output/ for a matching case record  
🔎 Check handbook/memory/ for relevant architectural decisions  
🧩 Use handbook/skills/domain-events.md if useful  
🏗️ Analyze whether the event represents a business fact  
⚠️ Detect coupling, timing, naming, or transactional risks  
✅ Give a clear recommendation  
📝 Update or suggest updating handbook/output/<case-name>.md  
📌 If the decision is stable, suggest moving it to handbook/memory/