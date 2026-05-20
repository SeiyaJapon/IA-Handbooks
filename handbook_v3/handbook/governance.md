# Handbook Governance

Operational conventions for maintaining the handbook as a system.

This is the single place for rules about how the handbook governs itself: what goes where, how it is maintained, and how it stays useful over time.

---

## Work Classification

Use the lightest workflow that fits. The default is to act. Structure is added only when it reduces risk or improves continuity — not to signal effort.

### Guardrails

- Trivial work stays trivial. Do not add structure to keep it feeling productive.
- Planning must reduce risk, not create paperwork.
- Steps can be incomplete when discovery is still happening.
- Milestones are for uncertainty or long-running work — not for every task.
- Ask for collaboration mode only when choosing wrong would be costly.

### User signals override everything

If the user indicates how they want to work — "let's think this through", "I'll define the approach", "I'm not sure yet", "walk me through options first" — follow that signal. The classification below only applies when no explicit signal is given.

### Classification

**Trivial work** — just do it.

Signals: small change, clear instruction, reversible, affects one place, low risk.

No output record needed. No plan needed. Act and report what changed.

**Non-trivial work** — use an output record.

Signals: multiple parts affected, may continue across sessions, some uncertainty about the path.

Pick a workflow from `handbook/workflows/`. Create an output record in `handbook/output/` using `handbook/templates/work-item.md`. Keep `Current State` up to date.

**Important or ambiguous work** — pause and ask.

Signals: architectural change, hard to reverse, no obvious path, multiple reasonable options, unclear scope, risk of making the wrong decision silently.

Before acting, offer the three collaboration modes:

> "This looks important or ambiguous. Do you want me to propose a plan and move forward with my best judgment, would you prefer to define the approach, or should we think it through together first?"

---

## Collaboration Modes

### Agent-led

The agent proposes the approach and moves forward. Useful when the user wants to delegate or prioritizes speed.

The agent must remain transparent about: assumptions made, risks identified, alternatives discarded, and how the result will be validated.

### User-led

The user defines the approach, priorities, or steps. The agent helps order, refine, document, and execute.

### Collaborative

Neither agent nor user has a clear path yet. The agent opens a design conversation:

- What options exist
- Pros and cons of each
- Risks and open questions
- Which decision to make first
- A recommendation with reasoning

The agent does not block or wait passively. It drives the conversation forward.

---

## Plan

A plan is optional. Use it when thinking before acting reduces risk or aligns expectations.

A plan can be brief. It should cover what is useful and nothing more:

- What we want to achieve
- Which parts of the system are affected
- Proposed approach
- Alternatives considered
- Known risks
- What will be validated
- What is out of scope

Do not write a plan for trivial work. Do not write a plan that is longer than the work it describes.

---

## Steps

Steps are a list of concrete, actionable items used when the work is clear enough to sequence.

Steps can be incomplete. If the work is still exploratory, add steps as they become clear — do not force a complete list upfront.

```markdown
## Steps

- [ ] Review current implementation
- [ ] Identify affected areas
- [ ] Propose minimal change
- [ ] Apply change
- [ ] Run validations
- [ ] Document result
```

Use steps for non-trivial work where the path is known but needs tracking.

---

## Milestones

Milestones group work into phases. Use them when steps would be premature or the work has multiple independent stages.

```markdown
## Milestones

### M1 — Understand current system
Status: in progress

### M2 — Decide target approach
Status: pending

### M3 — Implement first increment
Status: pending

### M4 — Validate and document outcome
Status: pending
```

Use milestones when: the work is long-running, the full shape is not yet known, or phases have different owners or timelines.

Do not use milestones for work that fits in steps.

---

## Output Organization

Records in `handbook/output/` must always be placed inside a category subfolder. Never directly in `output/`.

Before creating a new category:

1. List existing categories under `handbook/output/`
2. Reuse an existing category if it fits
3. Create a new category only when none fits — with a concrete, functional name
4. Create subcategories only when they improve discoverability

Do not create categories for one-off noise.
Do not flatten unrelated work into the same file just to avoid creating a new category.

Current categories: `audits`, `tasks`, `handbook-design`, `proposals`

Category purpose:
- `tasks` — work tasks (features, bugs, improvements) with a ticket or reference
- `proposals` — architectural or design proposals that are studied before acting (e.g. how to structure a new service, which pattern to adopt)
- `audits` — reviews of existing code, infrastructure, or processes
- `handbook-design` — decisions about the handbook itself

---

## Memory Organization

When adding or updating stable knowledge in `handbook/memory/`:

1. List existing files under `handbook/memory/`
2. Place the knowledge in the most appropriate existing file if one fits
3. Create a new file only when no existing file fits
4. Prefer stable, predictable names over overly specific ones

Do not create files for one-off noise.
Do not flatten unrelated knowledge into the same file just to avoid creating a new one.

---

## Memory Management

### What belongs in `memory/`

- Stable knowledge that is not directly inferible from the code
- Decisions that have been made and need to survive across sessions
- References the agent would otherwise have to rediscover each time

### What does not belong in `memory/`

- Work in progress or task state → use `output/`
- Detailed guidelines or explanations → use `skills/`
- Knowledge already covered by a skill → point to the skill, do not repeat it

### Entries are pointers, not explanations

A memory entry is short: one line that names a fact or points to a skill for detail. If a topic needs more than one or two lines to express, it belongs in a skill, not in memory.

✅ `Hexagonal architecture is the default for new backend services — see skills/hexagonal-boundaries`
❌ A ten-line explanation of when and how to apply hexagonal architecture

### When memory contradicts the code

The code is the source of truth. If a memory entry contradicts what is found in the code or in recent decisions:

1. Apply the current code or decision for the task at hand
2. Flag the stale entry and update it before closing the case

Do not leave stale entries unresolved. Memory that cannot be trusted is worse than no memory.

### No duplication with skills

If a skill already covers a topic in full, memory does not repeat it. The memory entry points to the skill.

---

## Architecture Decision Records

Write an ADR in `memory/architecture-decisions.md` when a decision made in a session:

- Affects more than one module, service, or repository
- Closes a valid alternative that could otherwise be revisited
- Has long-term consequences on structure, contracts, or dependencies

Write it before closing the session. It does not need to be long — context, decision, and consequences is enough. Use the template in `memory/architecture-decisions.md`.

Do not write ADRs for implementation details, bug fixes, or decisions that are obvious from the code.

---

## Repos Catalog Maintenance

When a session involves a repository not listed in `memory/repos-catalog.md`, add it before closing. Update the `Last verified` date whenever a repo is added or the catalog is reviewed.
