# SOLID Principles Skill

Evaluate code against SOLID **as forces, not as a checklist**. SOLID is a set of heuristics about change, coupling, substitutability, testability, cohesion, and boundaries. It is not a religion, not a quality stamp, and not an aesthetic standard for object-oriented code.

## When to use

Use this skill when:

- The user asks for a SOLID review.
- A code review surfaces evidence of change pain, tight coupling, broken substitutability, or hard-to-test code.
- A class is hard to change for one reason without affecting another.
- A subtype is being introduced and you need to verify it preserves the base contract.
- High-level code is importing low-level details directly and the question is whether to invert.
- A consumer is forced to depend on a fat interface and uses only a small slice.

## When not to use

Do not use this skill when:

- The user asked for "clean code" generally. Use `code-readability` first; SOLID is one input, not the whole answer.
- The user asked for an architecture review. Use `software-architecture`. SOLID is a code-level heuristic; it does not decide system structure.
- The code is short, throwaway, or a script. SOLID's value scales with code lifetime and reuse.
- The "violation" is purely aesthetic. SOLID violations matter when they tie to concrete change pain, coupling, incorrect substitution, or testability cost.
- The "fix" would introduce abstractions that solve no active force. Pre-emptive SOLID often costs more than it saves.

## Inputs to inspect first

Before judging a SOLID violation, inspect:

- **Real change patterns.** What has actually changed in this code recently? What is the next change likely to be? SOLID violations matter most where change happens.
- **Real consumers.** For ISP, who uses the interface? What methods do they actually call? For DIP, what wires the high-level module to the low-level one?
- **Test setup.** A class that requires huge fixtures, mocks of mocks, or infrastructure to test is a strong DIP/SRP signal.
- **The codebase's age and trajectory.** Early-stage code may legitimately have shallower abstractions. Long-lived code with frequent change cannot.
- **The team's skill ceiling.** A "fix" that introduces patterns the team does not understand is a regression.

If any of these is unknown, SOLID judgements are guessing.

## Hard rules (anti-dogma)

- **SRP does not mean "small class" or "one method".** It means one reason to change. A 300-line class with one axis of change is fine. A 30-line class that changes for three independent reasons is not.
- **OCP does not justify speculative abstractions.** OCP applies when the variation actually exists or is imminent. "We might need a strategy here someday" is not OCP; it is YAGNI bait.
- **LSP is about behavioural substitutability, not inheritance syntax.** A subtype can compile and pass `instanceof` checks while violating LSP (throwing where the base does not, weakening postconditions). Conversely, structural typing without inheritance can satisfy LSP.
- **ISP depends on real consumers.** A "fat" interface is fat **only** if some real consumer is forced to depend on methods it does not use. An interface used by one consumer with all methods is not an ISP violation.
- **DIP does not require a DI framework.** Manual constructor injection inverts dependencies just as well. A project can be DIP-correct without any container.
- **SOLID violations are findings only when tied to concrete pain.** Change pain, coupling pain, broken substitution, incorrect behaviour, or untestable code. Without one of those, the "violation" is aesthetic and the right answer is leave it.
- **Do not introduce interfaces, factories, or indirection unless they solve an active force.** Pre-emptive abstraction is often worse than the problem it pretends to solve.
- **Prefer simpler code when the abstraction has no demonstrated reason to exist.**

## How to work

1. **Confirm the trigger.** Is the user asking for a SOLID review, or is SOLID the supporting lens for a different question?
2. **Inspect the inputs above.** Real change patterns, real consumers, test setup, code lifetime.
3. **Walk the principles in this order**, with examples grounded in the actual code:
   1. **DIP** (highest practical impact). High-level code depending on concrete low-level details. Look for direct imports of database clients, HTTP libraries, SDKs, or framework specifics inside business code.
   2. **SRP** (high practical impact). Classes that change for multiple unrelated reasons. Look at recent commit history; if one class shows up in commits with very different intents, it has multiple reasons to change.
   3. **LSP**. Subtypes that throw where the base does not, no-op inherited methods, or require `instanceof` checks downstream.
   4. **ISP**. Fat interfaces where a real consumer uses only a slice. Need a real consumer to confirm.
   5. **OCP**. Long if/switch chains on a type discriminator that grow every time a new case appears. OCP is the most easily over-applied; require an actual or imminent variation.
4. **Tie each finding to concrete pain.** "This violates SRP" is not a finding; "this class changes both when the persistence schema moves and when the pricing rule changes; the last three commits each had to touch unrelated parts of it" is a finding.
5. **Propose the smallest change that resolves the pain.** Not the most elegant, not the most patterns-pure. The one that removes the active force.
6. **Explicitly leave alone violations that are not paying off in pain.** SOLID does not mandate fixes for aesthetic reasons.

## Output

Return findings as:

- **Concrete violations**: for each, name the principle, the file/class, the active force (change pattern, coupling, substitution, ISP consumer, OCP variation), the proposed minimal change.
- **Acceptable shortcuts**: where a principle is locally violated but the cost of fixing exceeds the benefit (early-stage code, throwaway, simple CRUD).
- **Architectural escalations**: when the violation is structural rather than local, route to `software-architecture` or the relevant architecture sub-skill.
- **Refusal to apply**: if a "violation" is aesthetic with no demonstrated pain, say so. Do not invent abstractions to satisfy the principle.

## Escalation

- If the violation is **structural** (high-level modules system-wide depending on low-level details, or layer boundaries broken across many files), load `software-architecture` and the relevant architecture sub-skill (`hexagonal-architecture`, `clean-architecture`, `onion-architecture`).
- If the violation is about **dependency direction at the layer level**, also load `dependency-injection`. SOLID's DIP is a code-level expression of the broader dependency rule that the architecture skills enforce.
- If the violation is about **how a domain is modelled** (an aggregate that mixes concerns, an entity that has escaped its responsibility), load `ddd` or `software-design/ddd`.
- If the violation is about **error handling shape** (exceptions everywhere, no error types), load `error-handling-patterns`.
- If the violation is about **how to refactor** without breaking things, load `refactoring-strategy`.
- If the user asked for a **language-specific review**, the SOLID lens is supporting; the language review skill is primary.

## What this skill does NOT do

- Decide architecture. SOLID does not pick hexagonal vs clean vs layered.
- Replace `code-readability`. Names, function size, and prose-like flow are a separate concern (often more impactful for daily work than SOLID).
- Mandate patterns. SOLID points at forces; the design patterns skill (and the team's judgement) decides whether a pattern resolves them.
- Validate aesthetic preferences. "I think this should be split into more classes" is not a SOLID finding without a concrete force.

## References

The original SKILL content (heuristics per principle, signals, examples) remains useful as a checklist when the operational discipline above has identified a candidate violation. See the inline `## Hard rules` and `## How to work` sections; per-principle heuristics:

- **SRP**: classes whose name contains "and"; classes changing for multiple unrelated reasons; the data-format change forces a touch in the same place as the business-rule change.
- **OCP**: a `switch` on a type string/enum in multiple places that must all be updated together; new cases require modifying every site.
- **LSP**: `instanceof` checks downstream of a polymorphic call; subtypes that throw where the base does not.
- **ISP**: interfaces with eight or more methods where implementations stub or throw on half; one consumer that uses only two of eight methods.
- **DIP**: `new DatabaseRepository()` inside a service class; `import knex from 'knex'` in a use case; framework decorators on business code.

These signals are useful only after the operational discipline confirms the principle's force is present. Without that, the signals produce false positives.
