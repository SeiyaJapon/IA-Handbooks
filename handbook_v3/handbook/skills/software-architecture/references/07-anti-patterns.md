# 07. Anti-patterns

Cross-architecture failures: errors that happen when an architecture is chosen, applied, or reviewed wrong, regardless of which architecture it is.

For architecture-specific anti-patterns, see the `08-anti-patterns.md` (or equivalent) of each sub-skill. This document is the cross-cutting list.

## Choosing-the-architecture anti-patterns

### Cargo cult architecture

The team picks an architecture because a famous company uses it, a popular blog post recommends it, or a framework documents it as its default.

- Why it fails: the forces of the famous company / blog / framework do not match yours. The architecture solves problems you may not have, at a cost you may not be able to absorb.
- How to recognise: the justification for the choice is "X does it" or "the framework recommends it", not the forces of the system.
- Fix: redo the decision starting from `02-how-to-choose-an-architecture.md`. The forces decide the architecture, not external authority.

### Resume-driven architecture

The team picks an architecture because someone wants experience with it, or because it looks good on the team's CV.

- Why it fails: the architecture matches the developer's career path, not the project's needs.
- How to recognise: the justification mentions skills, hiring, or career growth more than the system's forces.
- Fix: separate the two concerns. Career growth is legitimate; it is not an architectural justification.

### Trends-over-forces

"Microservices are modern, monoliths are legacy" or similar statements that mistake fashion for fit.

- Why it fails: trends are time-dependent; the system's forces are not. Picking by trend means re-picking every few years as the trend changes.
- How to recognise: the conversation references "modern", "legacy", "what everyone is doing", without naming the forces.
- Fix: list the forces. Re-evaluate.

### No decision

There is no architectural decision. The structure is whatever the first contributor wrote on the first day.

- Why it fails: architecture happens by accident. The next contributor follows what was there. A year in, the system has an implicit architecture nobody chose, with rules nobody enforces.
- How to recognise: ask "what is the architecture?". The answer is "we have layers" or "we use NestJS" or silence.
- Fix: make the implicit explicit. Document the architecture that exists. Decide if it is the right one. Migrate if it is not.

### Over-architecting

Pre-emptively adopting hexagonal, clean, microservices, EDA, CQRS, ES, and DDD for a small project that does not justify any of them.

- Why it fails: cost without benefit. The team spends weeks on infrastructure for a problem that did not need it.
- How to recognise: the architecture's complexity is disproportionate to the project's complexity.
- Fix: simplify. Pick the smallest architecture that fits. Add complexity when forces actually demand it.

### Under-architecting

A long-lived, rich-domain, multi-team system runs on a layered architecture with no enforced boundaries because "we will fix it later".

- Why it fails: cost compounds. Every quarter, "later" becomes "much later". By the time the team agrees to migrate, the migration cost is enormous.
- How to recognise: the system has been "we will fix it later" for more than a year.
- Fix: stop adding to the broken architecture. Start the migration. Use the strangler pattern. See `05-migration-paths.md`.

## Applying-the-architecture anti-patterns

### Architecture by folder

The team renames folders to match an architecture (`domain/`, `application/`, `infrastructure/`) without enforcing the dependency rules.

- Why it fails: the folders look architectural but the imports go anywhere. The system is the same as before, with new labels.
- How to recognise: a domain class imports from infrastructure, or an application class imports a concrete adapter, and the team calls the project "hexagonal".
- Fix: enforce the dependency rule. Lint, dep-graph, code review. Without enforcement, the architecture is decoration.

### Framework-as-architecture

The team adopts a framework's default structure and calls it "the architecture".

- Why it fails: the framework's default is its own opinion about structure, often layered or feature-based, often not what the project's forces require.
- How to recognise: the answer to "what is the architecture?" is the framework's name.
- Fix: separate framework from architecture. The framework lives in `infrastructure/`. The architecture is whatever rules the team enforces on top.

### Mixed organisational axes

Some folders at the root are layers (`domain/`, `application/`), some are bounded contexts or features (`auth/`, `product/`), some are framework modules (`prisma/`, `auth/`).

- Why it fails: two organisational axes in the same tree. New contributors do not know where to put new files. Imports become ambiguous.
- How to recognise: the root of `src/` mixes layer folders and feature folders.
- Fix: pick one organising axis (layer-first or context-first). Migrate the rest.

### Half-applied architecture

The architecture is partially applied: some modules follow it, some do not, and there is no plan to finish.

- Why it fails: the boundaries are not real if some code does not honour them. Reviewers cannot tell which rules apply where.
- How to recognise: a sentence like "this part is hexagonal but that part is still old".
- Fix: commit. Either finish the migration or roll back. Permanent half-states are worse than either fully-old or fully-new.

### Mixed architecture vocabularies

The codebase mixes hexagonal vocabulary with clean architecture vocabulary, or hexagonal with onion, or all three.

- Why it fails: confusion. New contributors must learn three vocabularies. Reviewers cannot agree on terms.
- How to recognise: the same concept has two names in different files. Some files use "ports", others use "interface adapters", others use "rings".
- Fix: pick one. Rewrite or rename the others.

## Disciplinary-confusion anti-patterns

### Design-as-architecture

"We are doing DDD, so the architecture is DDD." DDD is a design method; architecture is a separate decision.

- Fix: see `06-architecture-vs-other-disciplines.md`. DDD lives in the core; the architecture is what surrounds the core.

### Pattern-as-architecture

"The architecture is CQRS / Event Sourcing." CQRS and ES are patterns; architecture is the structure that hosts them.

- Fix: see `06-architecture-vs-other-disciplines.md`.

### Paradigm-as-architecture

"We are functional, so we don't need an architecture." The paradigm is a code-level decision; the architecture is a structural decision.

- Fix: separate the two. Both are needed.

### Deployment-as-architecture

"We deploy with Lambda, so the architecture is serverless." Lambda is the platform; serverless is the architecture if and only if the system is structured around functions as the unit of organisation.

- Fix: distinguish platform from architecture. A Lambda running a hexagonal app is hexagonal on serverless infrastructure.

## Reviewing-the-architecture anti-patterns

### Validation by folder name

A reviewer concludes "this is hexagonal because the folders are `domain/`, `application/`, `infrastructure/`".

- Fix: check the dependency rules. Folders are not enough.

### Validation by framework

A reviewer concludes "this is fine because we use NestJS / Spring / Laravel".

- Fix: framework is not architecture. Check the structural rules.

### Validation by pattern presence

"It is fine because we have repositories and use cases."

- Fix: the presence of artefacts does not validate the architecture. The dependency rules do.

### Validation by trend conformity

"It is fine because it matches what current literature recommends."

- Fix: validate against the project's forces, not against literature.

### "It works in production"

A system in production with broken architecture is still in production. That does not validate the architecture; it validates that the team has worked around the architecture.

- Fix: cost of working around the architecture is the architectural debt. Measure it (time spent on unrelated work, frequency of regressions, contributor onboarding time). Decide if migration is worth it.

## Migration anti-patterns

### Permanent half-state

A migration starts, makes some progress, stalls. The system is half in one architecture and half in another. There is no plan to finish.

- Fix: deadline + checkpoint + commitment. See `05-migration-paths.md`.

### Big-bang migration

The team plans an 18-month migration to a new architecture, then goes live. During those 18 months no new features ship, or features ship into the wrong place.

- Fix: strangler pattern. Each step is shippable. New features go into the new architecture from day one.

### Migration to satisfy fashion

The migration's justification is "the new architecture is more modern". No forces have changed.

- Fix: do not migrate. The cost of migration is high; the benefit must be a force change, not fashion.

### Migration without forces re-evaluation

The team migrates from layered to hexagonal because "the codebase has grown". The actual forces (team size, lifetime, change rate) have not been examined.

- Fix: re-evaluate forces before deciding the destination architecture. The cost of migrating to the wrong destination is double the migration cost.

## Output

When reviewing or designing architecture work, look for these patterns by name. Each is a finding. Cluster them in the review report by category (choosing, applying, disciplinary, reviewing, migration). The most damaging are the "no decision", "permanent half-state", and "framework-as-architecture" ones, because they invalidate the entire architectural conversation.
