# Workflow: Collaborative Planning

For important or ambiguous work where neither the agent nor the user has a clear path yet.

## When to use

- The goal is clear but the approach has multiple reasonable options
- The change is architectural or hard to reverse
- The user is not sure how to approach it
- Acting without alignment would be risky or wasteful

## How to work

This workflow is a conversation before it is an implementation.

1. Open the design conversation — do not propose a single answer immediately
2. Structure the problem space:
   - What options exist
   - Pros and cons of each
   - Risks and constraints
   - Open questions that must be answered before deciding
   - Which decision to make first (not all at once)
3. State a recommendation with reasoning — do not leave the user with only a list
4. Once alignment is reached, agree on a collaboration mode and move to `planned-change` or `investigation`

## Collaboration modes (choose before acting)

**Agent-led**: agent proposes approach and moves forward. Agent must surface assumptions, risks, and discarded alternatives.

**User-led**: user defines the approach. Agent helps order, refine, and execute.

**Collaborative**: design conversation continues until a clear path emerges.

## Creating the output record

Once a direction is agreed:

1. Create an output record using `handbook/templates/work-item.md`
2. Set `Collaboration Mode` to what was agreed
3. Capture the plan that emerged from the conversation
4. Define milestones or steps depending on the size and uncertainty of the work

## What to avoid

- Proposing a complete implementation before the approach is agreed
- Asking about every detail — only ask when the decision matters
- Leaving the conversation open-ended without a recommendation
- Treating this workflow as mandatory for every non-trivial task
