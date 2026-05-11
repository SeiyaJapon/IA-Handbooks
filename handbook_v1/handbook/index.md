# Handbook

## Startup

At startup, announce and read:

1. 🔎 Reading context: `handbook/index.md`
2. 🔎 Reading context: `handbook/governance.md`
3. 🔎 Reading context: `handbook/roles/default.md`
4. 🔎 Reading context: role referenced by `handbook/roles/default.md`
5. 🔎 Reading context: `handbook/skills/default.md`

After startup, load additional context only when relevant:

- Check `handbook/memory/` only when stable project knowledge may be relevant
- Check `handbook/output/` only when the task may continue previous work
- Read memory files only when relevant
- Read output records only when relevant
- Load extra roles only when relevant
- Load extra skills only when relevant

## Structure

1. `roles/` → reasoning lenses
2. `skills/` → reusable engineering capabilities
3. `commands/` → named workflows the developer activates explicitly
4. `memory/` → stable project knowledge
5. `output/` → ongoing work records

## Routing

- If the user asks to continue previous work, list `handbook/output/`
- If the task matches an existing work record, read it and continue from `Current State`
- If the task is new but likely to continue over time, create a new record in `handbook/output/`
- If the task is simple or one-off, do not create an output record
- If a specific technical capability is needed, load the relevant file from `handbook/skills/`
- If a different reasoning perspective is needed, load the relevant file from `handbook/roles/`
- If the developer invokes a command by name, load `handbook/commands/<command>/index.md` and follow its flow
- If the developer asks what commands are available, load `handbook/commands/index.md`
- When creating output or memory, choose the most appropriate category before writing

## Storage Organization

Before creating or modifying any file in `output/` or `memory/`, read and apply `handbook/governance.md`.

## References

- `governance.md`
- `roles/default.md`
- `skills/default.md`
- `commands/index.md`
- `memory/`
- `output/`

## Activity Traceability

When routing from this index, announce it:

🧭 Routing: `<reason>` → `<action>`

Then continue announcing file reads, role activation, skill loading, analysis, and output updates as defined in `AGENTS.md`.