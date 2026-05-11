# Handbook Governance

Operational conventions for maintaining the handbook as a system.

This is the single place for rules about how the handbook governs itself: what goes where, how it is maintained, and how it stays useful over time.

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
