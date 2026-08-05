# 08. Review Checklist

Operational checklist for reviewing an Onion Architecture codebase, after the cross-architecture review confirmed onion is in use.

The checklist uses onion's vocabulary throughout. If hexagonal or clean terms surface during review, that itself is a finding (vocabulary mixing).

## Step 1: Does onion apply?

1. Are the forces aligned with the inward-dependency family (rich domain, long lifetime)?
2. Does the domain have meaningful cross-entity behaviour worth a Domain Services ring?
3. Does the team's vocabulary speak onion (rings, Domain Services as a layer)?
4. Is the distinction between intrinsic domain logic and application orchestration valuable for this codebase?

**Stop condition.** If two are no, recommend hexagonal or clean instead.

## Step 2: Are the four rings identifiable?

1. Domain Model ring (entities, value objects).
2. Domain Services ring (cross-entity domain logic).
3. Application Services ring (use cases).
4. External Layer (infrastructure, framework, UI).

If any ring is missing, ambiguous, or duplicated, stop and report.

## Step 3: Is the dependency direction inward?

1. Domain Model depends on nothing.
2. Domain Services depends only on Domain Model.
3. Application Services depends on Domain Services and Domain Model only.
4. External Layer depends inward, never the reverse.
5. Are there inversions?
6. Is the rule enforced (lint, dep-graph, review)?

**Stop condition.** Inversions invalidate the architecture for the affected paths.

## Step 4: Are inner rings framework-free?

1. Are there ORM annotations on Domain Model?
2. Are there framework decorators on Domain Services or Application Services?
3. Does any inner ring import infrastructure types directly?

## Step 5: Is the Domain Services ring justified?

1. Is the ring populated, or empty?
2. Are the entries genuinely cross-entity domain logic, or are they orchestration / utilities / generic services?
3. Are Domain Services free of transactions, persistence, framework concerns?

If the ring is empty, recommend evaluating hexagonal or clean (siblings without an explicit Domain Services ring).

## Step 6: Are Application Services thin orchestrators?

1. Do Application Services orchestrate Domain Services and Domain Model?
2. Do they contain business rules that should be in Domain Services or entities?
3. Do they import infrastructure directly, or only through interfaces declared in the same ring?
4. Are interfaces (for repositories, external services, etc.) declared in Application Services, not in External Layer?

## Step 7: External Layer contained?

1. Is framework code confined to the External Layer?
2. Are repositories, clients, controllers in the External Layer, implementing inner-ring interfaces?
3. Are technology types (rows, ORM entities, raw HTTP) translated at the External Layer, not leaking inward?
4. Is wiring concentrated in a known location?

## Step 8: Vocabulary discipline

1. Is the codebase consistent in onion's vocabulary?
2. Are "ports", "driving/driven adapters", "Interactors", "Boundaries", "Controllers/Presenters/Gateways", "circles" appearing?

Vocabulary mixing is itself a finding. Pick one architecture and use its vocabulary.

## Summary

After all steps, produce:

- **Top three findings.** Direction violations and ring confusion outrank vocabulary slips.
- **Quick wins.** Move misplaced classes; remove ORM annotations from Domain Model; add lint rules.
- **Backlog.** Evaluate migration to hexagonal/clean if Domain Services is empty; refactor fat Application Services; consolidate vocabulary.
- **Confirmed strengths.**

## Forbidden conclusions

- "It is fine because the rings exist as folders."
- "It is fine because we use a popular framework."
- "It is fine because the domain has classes."
- "It is fine because we have ports and adapters." (That is hexagonal vocabulary; mixing.)
- "It is fine because we have Interactors and Boundaries." (That is clean vocabulary; mixing.)

The dependency rule, the ring partition (especially the Domain Services ring), and onion's vocabulary are what make onion. Folders, framework, and the presence of inner-ring code alone do not validate the architecture as onion.
