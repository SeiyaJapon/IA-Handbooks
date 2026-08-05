# 09. Review Checklist

This document is the operational checklist for reviewing an existing DDD design or codebase. It is used as the structured questionnaire when the user asks for a review.

The checklist follows the seven-step order of the skill. Steps are not optional and not reorderable. A FAIL on a critical question stops the review at that step. Continuing past a strategic FAIL produces tactical findings that may not survive the strategic correction.

## How to use this checklist

For each step:

- Answer every question.
- Mark each answer as PASS, FAIL, or UNCLEAR.
- For each FAIL or UNCLEAR, write one sentence describing the evidence.
- Apply the step's stop condition before moving on.

Do not jump ahead. If step 1 has not been answered, step 2 is not allowed. If step 3 has FAILs, step 5 is not allowed.

---

## Step 1: Does DDD apply?

1. Has DDD been determined to apply, or has it been imposed without justification (no behaviour beyond CRUD, no lifecycle, no business vocabulary, throwaway code)?

**Stop condition.** If DDD does not apply, the review ends here. Recommend a simpler architecture (transaction script, active record, layered without aggregates) and stop. Do not produce tactical or architectural findings about DDD if DDD itself is not the right choice.

---

## Step 2: Subdomains

1. Are the business capabilities of the system listed in business terms?
2. Are subdomains identified and grouped from those capabilities?
3. Is each subdomain classified as core, supporting, or generic, with the classification justified by the business?

**Stop condition.** If subdomains are not identified, or if there is no core/supporting/generic classification, stop and report. Without a subdomain map there is no basis to decide where to invest modelling effort, and every later finding will be relative to the wrong priorities.

---

## Step 3: Bounded contexts

1. Are bounded contexts identified as **business/model boundaries**, not folders, modules, microservices, queues, consumers, APIs, repositories, or schemas?
2. For each context, is there one clear ubiquitous language (one meaning per term, one term per concept)?
3. Are there terms used with two different meanings inside the same context, or different terms used for the same concept inside the same context?

**Stop condition.** If bounded contexts are not identified, or if any context has no clear ubiquitous language, stop and report. Tactical work depends on a context boundary; without one, aggregates and events have no home.

---

## Step 4: Context map

1. Is there a context map, and does it cover every pair of interacting contexts?
2. For each pair of interacting contexts, is the relationship type explicit (shared kernel, customer-supplier, conformist, ACL, open host, published language, partnership, separate ways)?
3. For each pair, is the failure mode documented (what happens to the downstream context when the upstream is down, slow, or wrong)?

**Stop condition.** If there is no context map, stop and report. Without it, cross-context coupling is invisible, and any cross-context finding in later steps will be incomplete.

---

## Step 5: Tactical model per bounded context

For each bounded context that survived steps 3 and 4, run this block:

1. Are aggregates defined from invariants, not from database tables?
2. For each aggregate, is the consistency boundary explicit?
3. Is the aggregate root the only entry point from outside the aggregate?
4. Are cross-aggregate references by ID, not by object?
5. Are aggregates of reasonable size (small enough to not contend, large enough to protect their invariants)?
6. Are entities **rich**, with behaviour and invariants enforced by their methods, or are they **anemic** (getters/setters with no behaviour)?
7. Are value objects used for concepts defined by their attributes (Money, Email, DateRange), or is there primitive obsession?
8. Are value objects immutable?
9. Are value objects validated at construction (a malformed value cannot exist)?
10. Are domain services genuinely cross-aggregate or stateless domain logic, or are they hiding behaviour that should live in entities?
11. Are domain events business facts in the past, owned by aggregates, immutable, with proper identity (`aggregateId`, `aggregateType`) and timestamp (`occurredOn`)?
12. Are domain events recorded by the aggregate, or raised by the application layer?
13. Are repositories defined per aggregate root, with domain-language interfaces, or are they generic CRUD on entities?
14. Are factories used only when construction itself contains business logic, or are they ceremony?

**Stop condition.** If aggregates are derived from tables, or if entities are anemic across the board, the tactical model is not DDD even if the layout suggests otherwise. Report and stop. Architectural findings on top of an anemic model are decoration.

---

## Step 6: Application layer (DDD perspective)

This step reviews the application layer **as a DDD concept**: orchestration only, no business rules. Architectural placement (where the application layer lives, what surrounds it, how it imports things) is not part of this step. For that, run the review of the architecture skill in use (`hexagonal-architecture`, `clean-architecture`, `onion-architecture`, etc.) after this one.

1. Are use cases / application services thin orchestrators, free of business decisions?
2. Are commands, queries, and results separate from domain entities?
3. Are use cases named after operations (verbs in imperative or query intent), not after generic terms ("ProcessRequest", "HandleEvent")?
4. Are transactions opened and closed in the application layer, not in the domain?
5. Are events dispatched after the transaction commits, not before?
6. Is structural validation done before or in the application layer, application validation in the use case, and domain validation in the aggregate?
7. Are domain errors, application errors, and external-system errors distinct types with distinct handling?
8. Are use cases free of business rules that should live in aggregates?
9. Are use cases free of god-object shape (one class with many unrelated operations)?

**Stop condition.** Failures here are findings, not blockers. Architectural findings (folders, ports, adapters, framework coupling) belong to the architecture skill's checklist, not this one. Continue to step 7 even if step 6 has FAILs, but record them.

---

## Step 7: Persistence and integration

1. Are events used correctly: domain events as business facts inside a context, integration events as cross-boundary communication?
2. Are integration events distinct from domain events, with their own published language?
3. Is communication between contexts done through events or published interfaces, never through direct imports of one context's classes from another context?
4. Is the broker delivery mode known and matched by consumer design (idempotency for at-least-once)?
5. If a transactional outbox is used, is the relay running and monitored?
6. If sagas are used, are they observable (can the team inspect a stuck saga)?
7. Is CQRS in use? If yes, is it justified by read/write asymmetry, or imposed by reflex?
8. Is Event Sourcing in use? If yes, is the event-versioning, snapshot, and replay strategy in place?
9. Is EDA in use? If yes, is the broker-operations maturity sufficient (DLQ, monitoring, replay)?
10. Are CQRS, Event Sourcing, and EDA introduced **only when justified**, with the reasons documented?

**Stop condition.** None. Step 7 is the last step. Findings here go straight to the summary.

---

## Summary

After all steps that ran, produce:

- **Top three findings** by impact. A failure at an earlier step beats a failure at a later step. A strategic FAIL beats a tactical FAIL beats an architectural FAIL.
- **Quick wins.** Changes that cost little and pay off immediately.
- **Backlog.** Changes that require planning (split a context, replace a shared kernel with events, introduce ACL, retire CQRS that is not justified).
- **Confirmed strengths.** Parts of the design that are sound. Naming what works prevents accidental damage in future refactors.

A review without these four items is a list, not a recommendation.

## Forbidden conclusions

A review must not conclude:

- "It is fine because the folders are well organised."
- "It is fine because the architecture (hexagonal, clean, onion) is in place." (Architecture is a separate decision; it does not validate the DDD model.)
- "It is fine because aggregates and repositories exist."
- "It is fine because CQRS / Event Sourcing / EDA is being used."

Each of those statements answers a different question than the one the review is asking. The question is: does the design model the domain correctly, with the right boundaries, the right invariants, and the right communication? Layout, framework choices, and trendy patterns are answers to other questions.
