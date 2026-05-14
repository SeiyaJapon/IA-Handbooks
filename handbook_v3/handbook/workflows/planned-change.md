# Workflow: Planned Change

For non-trivial work where the path is known but the change is large enough to benefit from structure.

## When to use

- Multiple files or services affected
- May continue across sessions
- Steps can be identified upfront or will emerge during execution
- Low-to-medium ambiguity — the goal is clear, the path is mostly known

## How to work

1. Check `handbook/output/` for an existing record on this topic
2. If none: create one using `handbook/templates/work-item.md` under the appropriate category
3. Write a brief plan if the scope justifies it — what is affected, what approach, what risks
4. Define steps as far as they are known — leave them incomplete if discovery is still happening
5. Execute step by step, updating `Current State` after each significant advance
6. Append to `History` when the session ends or a phase completes

## What to include in the output record

- Intent: what we are trying to achieve and why
- Plan: brief — affected areas, approach, risks (skip if the change is clear enough)
- Steps: actionable items, possibly incomplete
- Current State: updated after each meaningful advance
- History: one entry per session or significant decision

## When a plan is optional

Skip the plan section if:
- The steps already make the approach obvious
- The change affects only one service and has no architectural implications

## Escalate if

The work reveals significant ambiguity, an architectural decision, or a change that is harder to reverse than expected. Stop, surface it, and offer collaboration mode.
