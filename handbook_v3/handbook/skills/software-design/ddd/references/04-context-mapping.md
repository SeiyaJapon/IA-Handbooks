# 04. Context Mapping

A context map is the **explicit description of how bounded contexts relate to each other**. It captures the direction, the contract, and the political/organisational reality of each pair of contexts.

A context map is not a class diagram, not a database diagram, and not a service topology. It is a relationship diagram between regions of meaning.

## Why context maps matter

Without a context map:

- Coupling is invisible. Two contexts may be tightly bound through a shared assumption nobody wrote down.
- Integration decisions are reactive. Each pair of contexts negotiates ad hoc.
- Failure modes are hidden. Nobody knows what breaks in context A when context B is down.
- Boundaries erode. Without an explicit relationship type, the easiest path is direct import, which is the wrong path.

Drawing the map makes the coupling visible. Visible coupling is the only kind that can be managed.

## Direction

Each relationship between two contexts has a direction:

- **Upstream.** The context whose changes can force changes in the other.
- **Downstream.** The context that has to react to upstream changes.

Upstream is not about importance. It is about **influence**. The upstream context drives; the downstream context follows.

Identifying direction is the first step in classifying the relationship. Two upstream contexts cannot communicate without an explicit shared kernel or partnership. Two downstream contexts cannot communicate at all without an upstream mediating between them.

## Relationship types

The classical relationship types from DDD literature, with operational definitions.

### Shared Kernel

Two contexts share a small, jointly owned model.

- **When to use.** When two contexts genuinely need to refer to the same concept and the cost of duplication is higher than the cost of joint ownership.
- **Cost.** Every change to the shared kernel requires consent from every consumer. Speed of change is bounded by the slowest consumer.
- **Risks.** The kernel grows. The kernel becomes a dumping ground. The kernel becomes the bottleneck of the whole system.
- **Practical rule.** Use shared kernels rarely. Prefer events or published interfaces.

### Customer-Supplier

The customer is downstream and depends on the supplier. The supplier prioritises the customer's needs in its planning.

- **When to use.** When the customer cannot function without specific behaviour from the supplier, and the supplier accepts that responsibility.
- **Cost.** The supplier has obligations beyond its own roadmap.
- **Practical rule.** Make the contract explicit. Documented expectations, agreed change protocol.

### Conformist

The customer accepts the supplier's model verbatim, without negotiation.

- **When to use.** When the supplier is too distant or unwilling to negotiate (an external vendor, a legacy system the team does not control), and the cost of an ACL is not justified.
- **Cost.** The customer's domain becomes shaped by the supplier's choices. Vocabulary leaks in.
- **Risks.** The customer's model degrades over time as the supplier's model evolves.
- **Practical rule.** Use conformist relationships only when the alternative (ACL or change of supplier) is not feasible.

### Anti-Corruption Layer (ACL)

The customer translates the supplier's model into its own language, isolating itself from the supplier's vocabulary and instability.

- **When to use.** When the supplier's model is unstable, conflicts with the customer's language, or is owned by an external party that does not align with the customer's domain.
- **Cost.** Extra code in the customer's infrastructure. A translation layer to maintain.
- **Benefit.** The customer's model stays clean. Supplier changes are absorbed in one place.
- **Practical rule.** When in doubt, build the ACL. The cost is bounded; the cost of not building it grows.

### Open Host Service

A context publishes a stable, public interface that any number of consumers can use without bilateral negotiation.

- **When to use.** When a context is consumed by multiple downstream contexts and bilateral negotiation does not scale.
- **Cost.** The host commits to maintaining the interface. Breaking changes require versioning and migration.
- **Benefit.** Consumers can integrate independently.
- **Practical rule.** The interface is documented and versioned. Often paired with Published Language.

### Published Language

A documented, versioned interchange format (typically schema-defined events or a stable JSON contract) that contexts use to communicate.

- **When to use.** Whenever multiple contexts exchange messages and stability matters more than velocity.
- **Cost.** Schema discipline. Backward compatibility rules. Migration strategy.
- **Benefit.** The contract outlives the implementations on either side.
- **Practical rule.** The published language is owned. Someone is responsible for evolving it. It is not "whatever the producer happens to send".

### Partnership

Two contexts are jointly developed; their fate is bound. They succeed or fail together.

- **When to use.** When the cost of failure of one without the other is unacceptable, and the teams are willing to coordinate releases.
- **Cost.** Tight coupling at the planning level. Loss of independence.
- **Risks.** What was supposed to be a temporary arrangement becomes permanent. The two contexts become one in practice.
- **Practical rule.** Make the partnership time-bounded and revisit it. If two contexts must always ship together, they may be one context.

### Separate Ways

Two contexts intentionally do not integrate.

- **When to use.** When the cost of integration is higher than the cost of duplication, and the duplication does not cause inconsistency the business cannot tolerate.
- **Cost.** Some duplication of effort.
- **Benefit.** Each context evolves independently with no coupling cost.
- **Practical rule.** A valid choice. "We will not integrate these two" is a real architectural decision, not a failure to integrate.

### Big Ball of Mud

Recognising one is part of the map. A context that has lost its boundaries (everything imports everything, no consistent language, rules everywhere and nowhere) is a Big Ball of Mud.

- **What to do.** Quarantine it. Surround it with ACLs. Do not pretend it has structure it does not have. Plan for replacement, not refactor.

## How to build a context map

A practical sequence:

1. **List the contexts.** Names from `03-bounded-contexts.md`.
2. **For each pair of contexts that interact, identify direction.** Who drives, who follows.
3. **For each pair, identify relationship type.** From the list above.
4. **For each relationship, document the contract.** What flows across the boundary, in which direction, with what guarantees.
5. **For each relationship, document the failure mode.** What happens to the downstream context when the upstream is down, slow, or wrong.
6. **Mark the unhealthy ones.** Conformist relationships that should be ACLs. Shared kernels that grew. Partnerships that became one context.
7. **Plan changes.** Each unhealthy relationship is a backlog item.

The map is a living artefact. It changes as the system evolves. The first version is rough; iterate.

## How to draw the map

The visual format is secondary. What matters is that for each relationship the team has written:

- The two contexts.
- The direction.
- The relationship type.
- The contract (events published, interface methods, shared kernel scope).
- The failure mode.

Diagrams help communication. The text matters more than the picture.

## Anti-patterns in context mapping

- **No map at all.** The default. Coupling is whatever happens. Worst kind of architecture: invisible.
- **Map is a class diagram.** Confuses tactical and strategic. The map operates at the context level, not the class level.
- **Every relationship is a shared kernel.** The team treats shared kernels as the easy path. Eventually every context shares with every other context, and there are no contexts left.
- **Partnerships everywhere.** Two contexts must coordinate releases. Then three. Then five. The map degenerates into one big context.
- **No direction.** Every relationship is bidirectional. That is impossible in practice; it means the team has not thought about who drives.
- **No failure mode.** When upstream goes down, downstream "probably" handles it. That word is the problem.

## Output

A context map is complete enough to act on when, for every pair of interacting contexts, the team can answer:

- Which one is upstream?
- What relationship type connects them?
- What flows between them, in what format, with what guarantees?
- What happens when the upstream fails?
- Is this relationship healthy, or is it on the backlog to change?

Without those answers, the map is decorative.
