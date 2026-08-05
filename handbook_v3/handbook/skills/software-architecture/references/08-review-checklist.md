# 08. Review Checklist

This document is the operational checklist for reviewing the **architecture as a whole**, before drilling into the specific style. It is the cross-architecture review.

For style-specific review, see the `08-review-checklist.md` (or equivalent) of each sub-skill (`hexagonal-architecture`, `clean-architecture`, etc.).

The checklist follows a fixed order. Steps are not optional and not reorderable. A FAIL on a critical step stops the review at that step.

## How to use this checklist

For each step:

- Answer every question.
- Mark each answer as PASS, FAIL, or UNCLEAR.
- For each FAIL or UNCLEAR, write one sentence describing the evidence.
- Apply the step's stop condition before moving on.

Do not jump ahead. If step 1 has not been answered, step 2 is not allowed.

---

## Step 1: Is there an architecture at all?

1. Does the team have a documented or commonly understood architectural decision?
2. If asked "what is the architecture of this project?", do team members give consistent, structural answers (hexagonal, clean, microservices, etc.), not framework names or design methods?
3. Does the project distinguish between architectural decisions and operational/framework/design decisions?

**Stop condition.** If the answer to question 1 or 2 is no, the architecture is implicit and accidental. Stop and recommend making it explicit. Continuing the review past an implicit architecture produces findings about a structure nobody chose.

---

## Step 2: What architecture is actually expressed?

Independent of what the team says the architecture is, what does the codebase actually look like?

1. Is there a clear core (domain, business model) or is logic scattered across the code?
2. Where do dependencies point? Inward (hexagonal/clean/onion family), downward (layered), across (microservices), via events (EDA)?
3. Are there driving adapters and driven adapters, or are entry points and persistence directly tangled with the business code?
4. Is the codebase a single deployment unit (monolith), multiple independent services (microservices), or a function-based deployment (serverless)?
5. Does the structure support multiple driving mechanisms (HTTP, CLI, queue, schedule), or is it tied to one transport?

**Stop condition.** If the architecture observed differs from the architecture claimed, stop and report. The team is operating on a wrong mental model. Continuing produces findings the team will not recognise.

---

## Step 3: Is the architecture appropriate for the forces?

Re-evaluate the forces from `02-how-to-choose-an-architecture.md`:

1. Domain complexity: trivial, moderate, rich?
2. Expected lifetime: throwaway, mid-term, long-term?
3. Team topology: one person, single team, multiple teams, multiple contexts?
4. Change rate: slow, steady, asymmetric?
5. Scale: small, medium, large?
6. Operational/regulatory constraints (audit, residency, availability, compliance)?

Then ask:

7. Does the architecture match these forces?
8. If not, what is the gap (over-architecting or under-architecting)?

**Stop condition.** If the architecture is severely mismatched (long-lived rich-domain system on classical layered without enforcement; one-person hackathon on full microservices+EDA+CQRS+ES), the rest of the review is moot. Recommend redesign or migration.

---

## Step 4: Are the architecture's rules enforced?

Architectures that are documented but not enforced are decoration. Check:

1. Is the dependency rule enforced (review, lint, dep-graph)?
2. Are the boundaries between layers / contexts / services enforced (no cross-imports, no shared databases, no leaking types)?
3. Are violations caught at code review, or do they slip in?
4. Are there tests or tooling that fail when the architecture is violated?

**Stop condition.** Failures here are findings, not blockers. Continue to step 5 even if step 4 has FAILs, but record them. The fix is to add enforcement.

---

## Step 5: Are sibling architectures or disciplines being mixed unintentionally?

1. Does the codebase mix hexagonal vocabulary with clean architecture vocabulary?
2. Are there MVC controllers AND hexagonal driving adapters AND microservice fragments coexisting without a deliberate composition decision?
3. Is DDD vocabulary mixed with architectural vocabulary in confusing ways ("the architecture is DDD")?
4. Are patterns (CQRS, ES) treated as architecture?
5. Are paradigms (functional, OO) treated as architecture?
6. Is the framework (NestJS, Spring) treated as architecture?

**Stop condition.** None. Findings here go to the summary as confusion to fix.

---

## Step 6: Is the architecture consistent across the codebase?

1. Are all modules / contexts / services following the same architecture (or compositions decided deliberately)?
2. Is there half-migrated code (some modules in old architecture, some in new, no plan)?
3. Are there areas that drifted into a different shape than the rest (legacy corners, special-case modules)?

**Stop condition.** None. Findings here go to the summary, and may trigger migration planning.

---

## Step 7: Is the architecture sustainable?

1. Is the architecture documented in a way that new contributors can read and apply?
2. Is the documentation up to date?
3. Is the architecture reviewed regularly (a checkpoint, a review cadence) so it adapts to changing forces?
4. Does the team have shared vocabulary for architectural decisions?

**Stop condition.** None. Step 7 is the last step. Findings here go to the summary.

---

## Summary

After all steps, produce:

- **Top three findings** by impact. A failure at an earlier step beats a failure at a later step. Step 1 (no architecture) beats step 3 (wrong architecture) beats step 5 (mixed vocabularies) beats step 7 (sustainability).
- **Quick wins.** Changes that cost little and pay off immediately (document the implicit architecture, add a lint rule, rename a folder).
- **Backlog.** Changes that require planning (migrate to a different architecture, modularise before extracting microservices, add enforcement tooling).
- **Confirmed strengths.** Parts of the architecture that are sound. Naming what works prevents accidental damage in future refactors.

A review without these four items is a list, not a recommendation.

## Forbidden conclusions

A cross-architecture review must not conclude:

- "It is fine because we use a popular framework."
- "It is fine because we have layers."
- "It is fine because we are doing DDD / CQRS / Event Sourcing."
- "It is fine because it works in production."

Each of those answers a different question than the one the review is asking. The question is: **does the architecture match the forces of the system, and is it enforced?** Frameworks, layer presence, design methods, and uptime are answers to other questions.

## Drilling into a specific architecture

After this cross-architecture review:

- If the chosen architecture is hexagonal: load `hexagonal-architecture/` and run its `09-review-checklist.md`.
- If clean: load `clean-architecture/` and run its checklist.
- If onion: load `onion-architecture/`.
- If layered: load `layered-architecture/`.
- If MVC: load `mvc/`.
- If microservices: load `microservices-architecture/`.
- If serverless: load `serverless-architecture/`.
- If EDA: load `event-driven-architecture/`.

The cross-architecture review is the first pass. The style-specific review is the second.
