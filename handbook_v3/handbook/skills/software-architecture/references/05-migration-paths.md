# 05. Migration Paths

Real systems migrate between architectures. This document records the migration paths that recur in practice, the order of steps that keeps the system shippable during the migration, and the failure modes that turn a migration into a permanent half-state.

A migration without a plan becomes "we are halfway between architectures forever". The goal of this document is to prevent that.

## Principles

These principles apply to any architectural migration.

- **Pick the destination first.** Decide the architecture you are migrating to before starting. "We are migrating away from X" is not a migration plan; the destination must be specific.
- **Migrate in steps that keep the system shippable.** Each step is a working state. There is no "we will go live with the new architecture in 18 months". Continuous shippability is the constraint.
- **Migrate one boundary at a time.** Pick a context, a module, a service, or a layer. Migrate that. Then pick the next.
- **Strangler pattern.** The old structure runs in parallel with the new one. New work goes into the new structure. Old work is migrated piece by piece. Eventually the old structure is removed.
- **Enforce the new boundaries from day one.** As pieces move to the new architecture, the new dependency rules apply to them. Otherwise, half-migrated code drifts back to old habits.
- **Commit to the new vocabulary.** Once the destination is chosen, use its vocabulary in code, documentation, and conversation. Mixing old and new vocabularies extends confusion.
- **Set a deadline and a checkpoint.** Migrations that drift have no end. A checkpoint at three months ("are we still on plan?") prevents permanent half-states.

## Layered → Hexagonal / Clean / Onion

The most common migration: a classical layered codebase becomes inward-dependency. The destination decides the vocabulary; the steps are similar across the three.

### Steps

1. **Identify the core.** Find the business logic currently in the business or service layer. Decide which classes are the domain.
2. **Define the first port.** Pick one repository, identify what the domain needs from it (in domain language), and define a port.
3. **Implement an adapter for the existing data layer.** The adapter satisfies the port, internally calls what the data layer was doing.
4. **Invert the dependency.** The business layer now depends on the port (in the domain), not on the data layer directly.
5. **Move the business class to `domain/` or `application/`** as appropriate.
6. **Move the data layer class to `infrastructure/`** as the adapter.
7. **Repeat for every repository, every external client, every queue.**
8. **Invert the presentation layer.** The HTTP controller becomes a driving adapter. The business class it called becomes a driving port (use case).
9. **Delete the `presentation/` folder.** Move its contents to `infrastructure/http/`.
10. **Enforce the new dependency rule with tooling and review.**

### Common failure modes

- **Migrating folders before dependencies.** Renaming `presentation/` to `infrastructure/http/` without inverting the dependency is cosmetic.
- **Half-migrated: some code is hexagonal, some is layered, no plan to finish.** Either commit and finish or roll back.
- **Mixing vocabulary.** Hexagonal vocabulary in some files, clean architecture vocabulary in others. Pick one.

## Monolith → Microservices

The migration that goes wrong most often. The operational cost of microservices is real and high; the migration is rarely worth it for forces other than team scaling and asymmetric change rates.

### Steps

1. **Verify the forces.** Multiple teams? Asymmetric change rates? Independent release cadence needed? If not, do not migrate.
2. **Modularise inside the monolith first.** Identify bounded contexts. Establish module boundaries within the monolith. No cross-module imports.
3. **Stabilise the contracts.** Module-to-module communication uses contracts (events, well-defined interfaces) inside the monolith. The contracts will become network calls after extraction.
4. **Pick the first extraction.** Usually a context with few inbound dependencies, mid complexity, and a clear team owner.
5. **Extract.** Move the module to its own service. Replace in-process calls with network calls (events or HTTP) using the contracts already established.
6. **Operate the new service.** Deployment, monitoring, alerting, on-call. Fix the operational gaps before extracting the next.
7. **Repeat per context.**
8. **Stop when the forces are satisfied.** Not every module must become a service. A mostly-extracted system with two or three remaining modules in a monolith is a valid steady state.

### Common failure modes

- **Extracting before modularising.** Extracting tangled code produces a distributed monolith.
- **Shared database.** Two services reading/writing the same tables is a monolith with extra hops.
- **Synchronous chains across services.** Replicates monolith call patterns over the network. Cascading failures, latency stacking.
- **No operational maturity.** Extracting before the team can operate multiple services produces outages instead of velocity.
- **Migration with no end state.** "Eventually we will be all microservices" with no specific stopping condition.

## Layered → Microservices (without intermediate step)

Skipping the modularisation step. **Strongly discouraged.** The migration almost always becomes a distributed monolith.

If it is unavoidable, do not skip steps; do them on the new services as you extract them. But the cost is much higher than going through the intermediate step.

## Hexagonal → Microservices

When the team has been on hexagonal long enough for boundaries to be sound, microservices extraction is much easier: the boundaries are already enforced.

### Steps

1. Identify which contexts have to ship independently.
2. For each, the existing hexagonal boundary becomes the service boundary.
3. Replace in-process calls between contexts with network calls using events or published interfaces.
4. The internal hexagonal structure of each context becomes the internal architecture of each service.

### Notes

- The migration from hexagonal to microservices is mostly about **deployment and inter-service communication**, not about the architecture inside each unit.
- If the hexagonal boundaries were not enforced, this migration is the same cost as monolith → microservices and the same failure modes apply.

## Synchronous → Event-Driven

A migration of communication paradigm, not of internal architecture. A hexagonal codebase that calls another context synchronously can become event-driven without changing the inside of either context.

### Steps

1. **Identify the workflows that benefit from async.** Long-running, multiple consumers, decoupled lifecycle.
2. **Define the integration events.** Schema, naming, ownership.
3. **Add a publisher in the source context.** Driven adapter, transactional outbox if persistence is involved.
4. **Add a consumer in the destination context.** Driving adapter, idempotent handler, ACL if vocabulary differs.
5. **Run synchronous and async in parallel** for a release or two. Verify both produce the same result.
6. **Cut over.** Remove the synchronous path.

### Common failure modes

- **At-most-once messaging.** Loses events. Almost always wrong.
- **Consumers without idempotency.** At-least-once delivery duplicates effects.
- **Outbox without relay.** Events accumulate in the outbox and are never published.
- **Domain events used as integration events.** Internal vocabulary leaks across boundaries; refactors break consumers.

## Layered or Hexagonal → Serverless

Serverless changes how the driving adapter is dispatched, not the internal architecture.

### Steps

1. **Identify functions.** Each function is a serverless unit (or a small set of related units).
2. **Define the entry point as a driving adapter.** The function handler receives the event/request, calls the use case, returns the result.
3. **Split or keep the core.** If the workload fits one function, the core stays inside it. If it spans multiple functions, the core is shared (a published library or a per-function copy).
4. **Adapt persistence.** Connection pooling, cold starts, vendor-managed databases.
5. **Adapt observability.** Distributed tracing across functions.

### Common failure modes

- **Long-running work in a function.** Hits the time limit, fails partway.
- **Cold starts on critical paths.** Latency that the SLA does not allow.
- **Vendor lock-in without acknowledgement.** The function code couples to the vendor's invocation contract.
- **Distributed monolith of functions.** Functions calling functions synchronously, replicating monolith call patterns.

## MVC desktop → Web hexagonal

A common path when an old desktop app is rewritten as a web service.

### Steps

1. **Identify the model.** What was the model in MVC is candidate for `domain/` and `application/` of the new architecture.
2. **Identify the controller.** What was the MVC controller becomes the driving adapter (HTTP controller).
3. **Identify the view.** Discard or reimplement separately; the web frontend is its own concern.
4. **Add ports for what the controller used directly** (database access, file system, OS APIs).
5. **Build adapters for the new transport.**

This migration is essentially layered → hexagonal applied to a system that started as MVC.

## Refactor across siblings (HEX ↔ CLN ↔ ONI)

These migrations are mostly **cosmetic**: vocabulary changes, the underlying principles are the same.

### Cost

Low. The dependency rule is the same. The folder names change. The literature the team reads changes.

### When to do it

Only when the team's literature genuinely changes (a new lead's training, an external mandate). Otherwise, the cost-benefit favours staying with the current vocabulary.

### Steps

1. **Map vocabulary.** Hexagonal "ports" → Clean "interface adapters" (sort of), Onion "rings" of services.
2. **Rename folders.**
3. **Update documentation.**
4. **Update review checklists and tooling.**
5. **Train the team.**

## Output

When a migration is on the table, the team can produce:

- The destination architecture, picked specifically.
- The forces that justify the migration.
- The steps in order, each one shippable.
- The failure modes to watch.
- A deadline and a checkpoint.

Without these, the migration will drift. A drifted migration is the single biggest source of architectural debt in long-lived projects.
