# Status Presentation Skill

## When to use

The user asked for a panoramic view of in-flight work, agenda, status, or progress across projects.

## When not to use

- A focused report on a single review or finding: `report-output` or `prose-report`.

## How to work

1. Inventory the in-flight items.
2. Apply the project's visual format (sections + emojis 🔴🟡🟢🔵 + bold item) per Francisco's preference.
3. Avoid speculation; only items the inputs support.

## Output

A status view in the project's preferred format.

---

## Purpose

Present panoramic views of work status (agenda, in-flight items, blockers, pending decisions) in a format that is fast to scan and signals priority by visual weight, not prose.

## When to apply

Use this skill when the user asks for a panoramic view of the work, with prompts such as:

- "What are we working on"
- "Where are we"
- "Give me a summary"
- "What's pending"
- Any request that invites a multi-area status answer rather than a single tactical question

Do **not** apply this format for tactical answers (a single file, a single decision, a focused question). For those, stay concise and direct.

## Format

Group items by category. Each category opens with a **divider header** that flanks the title with long dash runs so the section break is impossible to miss when scrolling. Format:

```
━━━━━━━━━━━━━━ 🚧 En curso ━━━━━━━━━━━━━━
```

Rules for the divider:

- A blank line above and below the divider line.
- Around 14 box-drawing dashes (`━`) on each side of the title; exact count is not important, visual weight is.
- Emoji + space + title in the centre, between the two dash runs.
- One divider per section, never a plain `##` heading and never just bold (`**Title**`); both lose the visual break.

Standard categories and their emojis:

- 🚧 In progress
- 📥 Pending action or review
- 🧭 Open decisions
- 🛠️ Operational

Adjust categories to the actual situation, but always group and always use the divider format above. Avoid flat lists across the whole agenda.

Each item is a short block with three elements, in order:

1. **State symbol** (and any modifiers) at the start.
2. **Title in bold**, naming the item.
3. **One or two short context lines** below. No long paragraphs, no nested tables.

Avoid em dashes (—); use commas, colons, semicolons or parentheses instead.

Two-axis state system. State first (always), modifiers after. Convention: emoji + space + text.

State (always one):

| Emoji | Meaning |
|---|---|
| ▶️ | In progress |
| ✅ | Done |
| 🚫 | Blocked or critical |
| ⏸️ | Paused |

Modifiers (zero or more, after the state):

| Emoji | Meaning |
|---|---|
| ⚠️ | Attention: something to decide, change or watch; not critical |
| 🧪 | Out of production (done/validated but in staging or not yet shipped) |
| 🚀 | In production |

Examples: `✅ 🧪` done, pending ship to prod · `✅ 🚀` done and in prod · `▶️ ⚠️` in progress with things to change · `✅ ⚠️` done but with problems.

Do **not** use coloured circles (🔴🟡🟢🔵) as work-status markers. The user discarded them because the colour reads as an alarm for something that is merely "not finished". Severity tables for libraries or vulnerabilities do use 🔴🟠🟡🟢 as a separate severity scale; that is a different axis and is not affected by this rule.

Close the response with either a direct question ("Where do you want to start?") or a priority recommendation, never both.

## Anti-patterns

- Walls of bullet points without grouping.
- Tables with many columns: harder to scan than block format.
- Repeating the agenda file verbatim. The point is to surface signal, not transcribe.
- Using this format for narrow tactical answers.
- Mixing emoji systems mid-response.

## Activity Traceability

🔧 Loading skill: `status-presentation`
