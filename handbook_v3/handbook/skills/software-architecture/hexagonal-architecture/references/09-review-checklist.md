# 09. Review Checklist

This document is the operational checklist for reviewing an existing hexagonal architecture. It is used as the structured questionnaire when the user asks for a review.

The checklist follows the seven-step order of the skill. Steps are not optional and not reorderable. A FAIL on a critical question stops the review at that step. Continuing past a structural FAIL produces lower-level findings that may not survive the structural correction.

## How to use this checklist

For each step:

- Answer every question.
- Mark each answer as PASS, FAIL, or UNCLEAR.
- For each FAIL or UNCLEAR, write one sentence describing the evidence.
- Apply the step's stop condition before moving on.

Do not jump ahead. If step 1 has not been answered, step 2 is not allowed.

---

## Step 1: Does hexagonal apply?

1. Is there a core of business behaviour that is meaningfully independent from the framework, the database, and the transport?
2. Is the application expected to outlive its current technical stack, or to support multiple driving mechanisms (HTTP, CLI, queue, schedule)?
3. Does the team need fast, infrastructure-free tests of the core?

**Stop condition.** If hexagonal does not apply (a thin CRUD wrapper, a short-lived script, a system fully shaped by the framework), end the review and recommend a simpler architecture. Do not continue producing findings about layers and ports if the architecture itself is the wrong choice.

---

## Step 2: Application core

1. Is the application core identified?
2. Is the core framework-agnostic (no imports from frameworks, ORMs, HTTP libraries, SDKs, brokers)?
3. Is the core testable in isolation, with no infrastructure running?

**Stop condition.** If the core is not identified, or if it imports infrastructure, stop and report. Every finding below depends on a clean core; without one, ports and adapters are decoration.

---

## Step 3: Driving ports

1. Are driving ports defined as interfaces in `application/` (or `domain/` when domain-owned)?
2. Are driving ports named after operations in domain language (`CreateScenarioUseCase`, `EvaluateRiskUseCase`), not after technical concepts (`ProcessRequestHandler`)?
3. Is each driving port a single operation, or a small set of related operations that belong together?
4. Does no driving port live in `infrastructure/`?

**Stop condition.** If driving ports are missing, or if they live in `infrastructure/`, stop and report. There is no boundary on the input side without them.

---

## Step 4: Driven ports

1. Are driven ports defined as interfaces in `application/` or `domain/`?
2. Are driven ports named after capabilities in domain language (`OrderRepository`, `RiskScoreProvider`), not after technologies (`PostgresClient`, `HttpFetcher`)?
3. Does each driven port abstract one capability that the core needs?
4. Does no driven port live in `infrastructure/`?
5. Is there no generic `Repository<T>` or `Client<T>` shape in the core?

**Stop condition.** If driven ports are missing, or if they expose technology-shaped methods, stop and report. The core is leaking outward.

---

## Step 5: Adapters

For each driving port:

1. Is there at least one driving adapter (HTTP controller, CLI handler, queue consumer)?
2. Does the driving adapter live in `infrastructure/`?
3. Is the driving adapter thin and free of business rules?
4. Does the driving adapter translate input and output between the protocol and the port's vocabulary?

For each driven port:

5. Is there at least one driven adapter?
6. Does the driven adapter live in `infrastructure/`?
7. Does the driven adapter implement the port without leaking SDK or technology types?
8. Does the driven adapter translate the technology's errors into the core's error vocabulary?
9. Does the driven adapter own no business rules?

**Stop condition.** Findings here are localised. Continue to step 6 even if step 5 has FAILs, but record them.

---

## Step 6: Dependency direction

1. Does `domain/` depend on nothing from `application/` or `infrastructure/`?
2. Does `application/` depend only on `domain/` and on port interfaces?
3. Does `infrastructure/` depend on `application/` and `domain/`, never the reverse?
4. Are there any imports from a concrete adapter to a use case, or from a domain class to an SDK or framework?
5. Is the dependency rule enforced (lint rules, dependency graph, code review), or only documented?

**Stop condition.** A FAIL on questions 1, 2, or 3 invalidates the architecture for the affected paths. Stop and report. Continuing the review while the direction is broken produces tactical findings on top of a structural failure.

---

## Step 7: Layout

1. Are the only top-level layers `domain/`, `application/`, `infrastructure/`?
2. Is there no `presentation/` folder?
3. Are framework modules (NestJS, Spring, Laravel) inside `infrastructure/`, not at the root of `src/`?
4. Is the organising axis consistent (bounded-context-first or layer-first), with no mixing of axes?
5. Inside `infrastructure/`, are subfolders created only when there is more than one file of the kind (no single-file `http/` folder, no single-file `persistence/` folder)?
6. Are there empty `domain/` or `application/` folders with only a README? (There should not be.)
7. Is the composition root (where adapters are wired to ports) clearly identifiable?

**Stop condition.** None. Step 7 is the last step. Findings here go to the summary.

---

## Summary

After all steps that ran, produce:

- **Top three findings** by impact. A failure at an earlier step beats a failure at a later step. A core failure beats a port failure beats an adapter failure beats a layout failure.
- **Quick wins.** Changes that cost little and pay off immediately (rename `presentation/` to `infrastructure/http/`; move a port out of `infrastructure/`; delete an empty layer).
- **Backlog.** Changes that require planning (introduce missing ports for current direct dependencies; replace generic repository with domain-named ones; enforce the dependency rule with tooling).
- **Confirmed strengths.** Parts of the architecture that are sound. Naming what works prevents accidental damage in future refactors.

A review without these four items is a list, not a recommendation.

## Forbidden conclusions

A hexagonal review must not conclude:

- "It is hexagonal because the folders are `domain/`, `application/`, `infrastructure/`."
- "It is hexagonal because there are interfaces in the code."
- "It is hexagonal because the framework is NestJS."
- "It is hexagonal because we use DDD inside."

Hexagonal is the dependency rule and the ports/adapters separation. Folders, interfaces, framework choice, and the design method inside the core do not, on their own, make a system hexagonal.
