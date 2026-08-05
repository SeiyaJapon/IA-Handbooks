# 07. Review Checklist

This document is the operational checklist for reviewing an existing layered architecture. Use it after the cross-architecture review (`software-architecture/references/08-review-checklist.md`) has confirmed that the architecture in use is layered.

The checklist follows a fixed order. Steps are not optional and not reorderable.

## How to use this checklist

For each step:

- Answer every question.
- Mark each answer as PASS, FAIL, or UNCLEAR.
- For each FAIL or UNCLEAR, write one sentence describing the evidence.
- Apply the step's stop condition before moving on.

---

## Step 1: Does layered apply?

1. Is the domain moderate (forms-over-tables with some rules), not rich?
2. Is the lifetime mid-term, not long-term?
3. Is the team small, single context?
4. Are there fewer than three driving mechanisms (HTTP, CLI, queue, etc.) sharing the same business logic?

**Stop condition.** If two or more of these are no, layered may be the wrong architecture. End the review and recommend a migration evaluation (see `software-architecture/references/05-migration-paths.md`).

---

## Step 2: Are the layers identified?

1. Are the layers explicit (presentation, business, data, plus any variant additions)?
2. Does each layer have a documented responsibility?
3. Do team members agree on which layer each existing class belongs to?

**Stop condition.** If layers are not identified or contributors disagree on placement, the architecture is implicit. Stop and recommend documenting the layers and their responsibilities first.

---

## Step 3: Is the dependency direction respected?

1. Do dependencies flow downward only (presentation → business → data)?
2. Are there cases where presentation calls data directly, skipping business?
3. Are there reverse dependencies (data calls business, business calls presentation)?
4. Are there cycles in the dependency graph between layers?
5. Is the direction enforced (lint rules, dep-graph, code review), or only documented?

**Stop condition.** A FAIL on questions 1, 2, 3, or 4 invalidates the architecture for the affected paths. Stop and report.

---

## Step 4: Is each layer doing its own job?

1. Are presentation classes free of business rules?
2. Are business classes free of transport types (HTTP, CLI) and storage types (SQL, ORM)?
3. Are data classes free of business decisions?
4. Are framework concerns confined to presentation and data?
5. Are there fat controllers, smart repositories, or anaemic services?

**Stop condition.** None. Findings here are placement violations to fix.

---

## Step 5: Are types leaking across layers?

1. Does the business layer return database types (rows, ORM entities) to the presentation?
2. Does the presentation layer accept business or domain types directly as wire format (without DTO mapping)?
3. Does the business layer accept HTTP types as parameters?
4. Are repositories returning generic query results that the business has to interpret?

**Stop condition.** None. Findings here predict architectural decay; record them and consider migration to hexagonal/clean/onion if they recur.

---

## Step 6: Variant fit

1. If the codebase uses variants (service layer, integration layer, infrastructure layer, application layer), is each variant justified by an actual concern?
2. Are there layers that exist only as ceremony, with pass-through code or near-empty contents?
3. Are framework-imposed defaults respected, or are they overridden inconsistently?

**Stop condition.** None. Findings about ceremony go to the summary.

---

## Step 7: Sustainability and migration readiness

1. Is the architecture documented?
2. Is the documentation up to date?
3. Is the dependency rule enforced with tooling?
4. If the system is approaching the conditions where layered no longer fits (rich domain, long lifetime, multi-team, multi-transport), is there a migration plan or a checkpoint to evaluate one?

**Stop condition.** None. Step 7 is the last step. Findings here go to the summary.

---

## Summary

After all steps, produce:

- **Top three findings** by impact. Direction violations and type leaks beat placement violations beat ceremony.
- **Quick wins.** Add a missing dep-graph rule, move a misplaced class, delete an empty layer.
- **Backlog.** Restructure a fat controller, move business rules from the data layer, plan a migration.
- **Confirmed strengths.** Parts of the architecture that are sound.

A review without these four items is a list, not a recommendation.

## Forbidden conclusions

A layered review must not conclude:

- "It is fine because the folders are presentation/, business/, data/."
- "It is fine because we use the framework's defaults."
- "It is fine because there are no compile errors."

The dependency direction and the layer responsibilities are what make the architecture layered. Folders, framework, and compilation do not, on their own, validate the architecture.
