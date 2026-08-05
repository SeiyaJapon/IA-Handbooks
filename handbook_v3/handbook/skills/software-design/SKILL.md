# Software Design Skill

Use this skill when discussing, designing, reviewing, or refactoring **the design of software at the code level**: how the inside of a component is modelled, how classes and functions are shaped, what patterns apply, what principles guide the structure of code.

This skill is the entry point for everything design-related. It does not replace specific design skills (`ddd`, `solid-principles`, `design-patterns`, `dependency-injection`, `refactoring-strategy`, `error-handling-patterns`, `concurrency-patterns`, `subprocess-management`, `cli-design`). It governs how those skills are reached and used together.

## What this skill is for

- Decide whether the case is a design decision (vs architecture, vs pattern, vs paradigm).
- Pick which design method or principles apply (DDD, SOLID, GoF, DI, etc.).
- Coordinate decisions across design disciplines.
- Review the design of a component or module as a whole.

When the case is clearly inside one specific design skill, load that directly. This skill is for cross-discipline design decisions.

## Non-negotiable rule

Never start design work from "what classes to create" or "which pattern to apply".

The mandatory order is:

1. State what kind of decision is on the table (design vs architecture vs pattern vs paradigm).
2. Identify the **forces** at the design level: domain richness, change frequency, testing requirements, team familiarity.
3. Pick the design method that fits (DDD for rich domains, transaction script for thin ones, active record for shaped-by-DB code).
4. Apply principles (SOLID, code readability, DRY where it pays).
5. Apply patterns where they reduce complexity, not as ceremony.
6. Refactor when forces change.

## Hard rules

### Design is the inside, not the structure

Architecture decides the structure of the system. Design decides how the inside of each component is shaped. Confusing the two produces "designs" that are really architectural decisions or vice versa.

### Patterns are tools, not goals

Design patterns (Strategy, Factory, Observer, etc.) are reusable techniques. They are applied when the problem genuinely matches the pattern, not because the team likes patterns.

### Principles before patterns

SOLID, code readability, single responsibility, dependency inversion. These are guiding principles. Patterns are instances of these principles applied to specific problems.

### Refactor when forces change

Design that fit yesterday's forces may not fit today's. Refactoring is part of design discipline, not a separate activity.

## Forbidden shortcuts

- "We use design patterns, so the design is good."
- "DDD is the design."
- "SOLID makes everything correct."
- "If we apply enough patterns, the code will be clean."
- "Design and architecture are the same thing."

## Mandatory review behavior

When reviewing a design, check in order:

1. Is the design appropriate for the domain's complexity?
2. Are SOLID principles respected (or deliberately violated for a reason)?
3. Are patterns applied to real problems, or as ceremony?
4. Is the code readable: names, function size, single responsibility?
5. Is dependency injection used to keep components testable?
6. Is error handling explicit and consistent?
7. Is concurrency handled correctly when applicable?

## If the user asks for "the design of this module"

Do not propose patterns or class hierarchies until the design forces are clear.

First produce:

- The domain richness in this module (rich, moderate, thin).
- The expected change frequency.
- The testing requirements.
- The design method (DDD, transaction script, etc.).
- The principles that apply (SOLID, readability).
- The patterns that fit.

## Design vs neighbouring disciplines

- **Design** is the inside of a component.
- **Architecture** is the structure of the system (see `software-architecture/`).
- **Patterns of architecture** (CQRS, Event Sourcing, EDA): structural techniques (see `architecture-patterns/`).
- **Paradigms** (OO, functional, procedural): code-level approaches.
- **Design patterns** (GoF, behavioural, structural, creational): code-level techniques (see `software-design/design-patterns/`).

When in doubt, classify the question first. If it is about how the inside is shaped, this skill applies.

## References

- `references/01-what-is-software-design.md` for what design is, what it is not, vs architecture.
- `references/02-design-methods-overview.md` for DDD, transaction script, active record, table module.
- `references/03-principles-overview.md` for SOLID, code readability, DRY, KISS, YAGNI.
- `references/04-when-to-apply-patterns.md` for the difference between pattern usage and pattern ceremony.
- `references/05-relationship-with-architecture.md` for design vs architecture, design inside an architecture.
- `references/06-anti-patterns.md` for cross-discipline design failures.
- `references/07-review-checklist.md` for reviewing a design as a whole.
- `references/08-user-preferences.md` for Francisco's project conventions.

## Sub-skills (specific design disciplines)

Each specific discipline has its own skill at the same level as this `SKILL.md`:

- `ddd/`: Domain-Driven Design for rich domains.
- `solid-principles/`: SOLID at the class and function level.
- `design-patterns/`: GoF and modern patterns.
- `dependency-injection/`: DI as a design technique (manual or via a container).
- `refactoring-strategy/`: when and how to refactor.
- `error-handling-patterns/`: explicit, consistent error design at the function/module level. Cross-cutting boundaries (domain, integration, EDA, observability) are covered by their own skills.
- `concurrency-patterns/`: when concurrency is a design concern.
- `subprocess-management/`: child processes, signals, streams.
- `cli-design/`: command-line interfaces, exit codes, help.

Each sub-skill is **complete on its own**. Load the sub-skill directly when the question is inside it.

Use this mega-skill when the question is **about choosing across disciplines, comparing, or reviewing a design as a whole**.
