# Workflow: Investigation

For exploratory work where the goal is to understand before acting.

## When to use

- The problem is not well-understood yet
- The change cannot be designed without first reading and analyzing existing code
- There is significant uncertainty about scope, approach, or feasibility
- The task is to produce a finding, proposal, or recommendation — not necessarily code

## How to work

1. Create an output record using `handbook/templates/work-item.md` under `handbook/output/proposals/` or `audits/`
2. Set Collaboration Mode based on what was agreed or inferred
3. Define milestones if the investigation has multiple phases; otherwise use steps
4. Investigate — read, trace, analyze, document findings as you go
5. Produce a conclusion: what was found, what is recommended, what remains open
6. Update `Current State` and `History` throughout

## Structure for the output record

Steps can be incomplete or emergent — write them as you discover the shape of the investigation:

```
- [ ] Understand current implementation
- [ ] Identify constraints and dependencies
- [ ] Map affected areas
- [ ] Evaluate options
- [ ] Document findings and recommendation
```

## What makes a good investigation output

- Findings are grounded in evidence, not assumptions
- Open questions are named explicitly
- Recommendations include trade-offs
- Out-of-scope items are stated

## When to stop investigating and start acting

When the path forward is clear enough that uncertainty no longer justifies delay. Name that moment explicitly and reclassify to `planned-change`.
