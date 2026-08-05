# 03. Bounded Contexts

## Definition

A bounded context is a **semantic boundary** within which a specific model and its ubiquitous language are consistent and authoritative.

Inside a bounded context:

- Every term has exactly one meaning.
- Every rule is enforced by the model.
- Every concept exists at one level of detail decided by the context's purpose.

Outside the context, the same terms can mean other things. That is expected and not a problem, as long as nothing crosses the boundary without translation.

## What a bounded context is not

- **Not a folder.** A folder is a packaging convenience.
- **Not a NestJS module, a Spring bean, a Laravel service provider, or any framework artefact.**
- **Not a package.** A package is a unit of code distribution. A context may correspond to one package, several, or none.
- **Not a namespace.** Same reason as folders.
- **Not a microservice.** A microservice is a deployment unit. A bounded context can live in a monolith with other contexts and still be a context. A microservice that contains two bounded contexts is two contexts in one process.
- **Not a repository.** Two contexts can share a Git repository (a monorepo) and still be two distinct contexts. One context can also span several repositories. The Git repository is operational packaging, not the boundary.
- **Not a database schema.** Two contexts that share a database table share a model whether they want to or not, which means they are one context with the boundary mislabelled.
- **Not a queue, topic, or event stream.** A queue is a transport mechanism. A bounded context can publish or consume from a queue without being one, and the same queue can be used by several contexts.
- **Not a consumer or worker.** A consumer is a runtime instance that processes messages. A bounded context can have zero, one, or many consumers, and a consumer is not a context by itself.
- **Not an API.** An API is the external surface of a context (or of several contexts). The boundary is the model behind it, not the surface.
- **Not a team.** A team can own one or more contexts; a context can be owned by one team. The ownership and the boundary are related but distinct.

The structure of the code may **reflect** bounded contexts (folders named after them, modules grouped by them), but the structure does not **define** them. The boundary is conceptual; the structure is its shadow.

## The three properties of a bounded context

A bounded context has three properties that are not negotiable. If any one is violated, the boundary is fictional.

### 1. One language per context

Inside the context, every business term has one meaning. The same word does not denote two concepts. The same concept does not have two names. The team, the code, the database columns, the API contracts, and the conversation with domain experts all use the same words with the same meaning.

If the same word naturally refers to two different things, that is a sign that the boundary is misplaced and must be split, or that one of the two meanings belongs in a different context.

### 2. Models do not cross the boundary

A class, type, value object, or aggregate defined in one context is not imported, referenced, or extended by another context.

This rule has no exceptions in the code:

- Not even within the same monorepo.
- Not even within the same process.
- Not even when "it would be so simple to just import this".

The reason is that the model is the embodiment of the language. If a class crosses the boundary, the language crosses with it, and the receiving context now contains two languages competing for the same words.

### 3. Communication is explicit

Contexts communicate only through:

- **Domain events** (asynchronous): a context publishes a fact in past tense; other contexts subscribe.
- **Published interfaces** (synchronous): a context exposes a stable contract that other contexts call as if it were a remote service, with timeouts, failure handling, versioning.
- **Shared kernel** (rarely): a small, jointly owned model that both contexts depend on as if it were a third party.

Anything else is a violation. Reading the other context's database directly, calling internal services, sharing global state, or mutating files written by another context all break the boundary.

## How to identify bounded contexts

Identification happens during strategic design. The signals to look for:

- **Vocabulary boundaries.** Where the same word means different things, the boundary lives there.
- **Rule boundaries.** Where one set of rules ends and another begins, the boundary lives there.
- **Lifecycle boundaries.** Where a concept's lifecycle is owned by one team and consumed by another, the boundary lives there.
- **Change rate boundaries.** Two regions of the model that change at very different rates often belong in different contexts; coupling them slows the fast one to the speed of the slow one.
- **Stakeholder boundaries.** Where two business stakeholders own different parts of what was modelled as one thing, the boundary lives there.

Identification is iterative. The first cut is a hypothesis. As the team learns the domain, the boundary moves.

## How to draw the boundary

Once a context is identified, draw the boundary explicitly:

1. **Name the context.** The name reflects the business capability or the model it owns. Not a technical word.
2. **State its purpose.** One sentence in business terms. "Owns the simulation of risk scenarios for receivables."
3. **List its language.** The terms that have a specific meaning inside this context, with their definitions.
4. **List its aggregates and core entities.** What lives here.
5. **List what it publishes.** Events, queries, commands accepted from outside.
6. **List what it consumes.** Events from other contexts, queries to other contexts, external services.
7. **Decide its relationship with each other context.** See `04-context-mapping.md`.

Without these seven items written down, the boundary exists only in the head of whoever drew it. That is not a boundary.

## Shared kernel

When two contexts genuinely need to share a piece of the model, that piece becomes a **shared kernel**: a small, jointly owned region that both contexts depend on as if it were a third party.

Shared kernels carry rules:

- **Small.** A shared kernel that grows is a sign that the boundary between the two contexts is wrong.
- **Jointly owned.** Every change requires the consent of every consumer. No unilateral changes.
- **Treated as a third dependency.** The kernel has its own versioning, its own change log, its own contract.
- **Not a dumping ground.** "Things we did not know where to put" do not go in the shared kernel. They go in the context where they belong, even if that requires deciding.

Shared kernels are the exception, not the rule. Most context pairs should communicate by event or by published interface, not by shared model.

## Anti-corruption layer

When a context needs to consume a model from another context (or from an external system) without letting that model leak into its own language, it builds an **anti-corruption layer (ACL)**.

The ACL is a translation boundary inside the consuming context's infrastructure. It:

- Reads the foreign model.
- Translates it into the consuming context's own language.
- Hides the foreign vocabulary from everything inside the consuming context.

The ACL adds ceremony. It is worth the cost when:

- The foreign model is unstable and you do not want its churn to ripple through your code.
- The foreign model uses vocabulary that conflicts with yours.
- The foreign model is owned by an external party that does not negotiate with you.

The ACL is not optional cosmetics. It is a deliberate isolation layer.

## Multiple contexts in one repository

When several bounded contexts live in the same repository, the boundary is enforced by convention, code review, and tooling, not by the file system.

Conventions that work in practice:

- Each context has its own top-level folder with a name that signals it is a context (suffix, prefix, or a clear naming convention).
- Cross-context imports are forbidden. Tooling (lint rules, dependency graphs) blocks them.
- Each context owns its database tables. No table is read or written by two contexts.
- Each context publishes its events through the shared event infrastructure; consumers subscribe and translate.
- Each context exposes its synchronous interface (if any) through a stable module that other contexts consume as if it were external.

A monorepo with bounded contexts treats every context as a separate library. The fact that they ship together is operational; the fact that they are independent is architectural.

## Anti-patterns

- **Each module is a context.** Mechanical mapping. Modules are technical; contexts are semantic.
- **Each microservice is a context.** Same problem. A microservice is a deployment unit.
- **Each entity is a context.** Even worse. The boundary is a region of the model, not a single object.
- **Sharing a database between contexts.** The schema couples them at the lowest level, and no amount of code discipline reverses that.
- **Importing across contexts because "it is the same repo".** Same repo is operational; the boundary is conceptual. The repo does not authorise crossing it.
- **Discovering contexts by drawing folders.** The folder structure is the result of the context decision, not the input.

## Output

When a context is well defined, the team can answer for it:

- What is its name and purpose?
- What is its language?
- What does it own?
- What does it publish?
- What does it consume?
- What is its relationship with every other context?

If any of these has no answer, the context is not finished.
