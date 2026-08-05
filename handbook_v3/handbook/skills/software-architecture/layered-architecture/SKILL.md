# Layered Architecture Skill

Use this skill when designing, reviewing, or refactoring software using classical Layered Architecture (presentation, business, data layers with downward dependencies). Especially when the user mentions n-tier, three-tier, layered, business layer, data access layer, or DAO.

## What layered is

Layered Architecture is the classical structural pattern for enterprise software, dominant before the rise of the inward-dependency family (hexagonal, clean, onion).

The system is divided into stacked layers, each with a specific responsibility. Layers communicate strictly downward: the upper layer calls the next one below. The most common shape is **three-tier**: presentation, business, data. Variants add layers (services, integration, persistence) but keep the downward direction.

Layered is an **architecture**, not a design method. It tells you how the system is structured. It does not tell you how the code inside each layer is modelled.

## What layered is not

- Not a synonym of hexagonal, clean, or onion. Those reverse the dependency direction; layered is the predecessor they react against.
- Not a synonym of MVC. MVC structures the presentation side; layered structures the whole system.
- Not a folder template. The dependency direction is what makes layered layered.
- Not always a bad choice. For mid-term, framework-driven, moderately complex systems, layered is a reasonable architecture.

## Non-negotiable rule

Never start layered work from frameworks or transports.

Always start from the business behaviour: what the system does, what data it manages, who calls whom.

The mandatory order is:

1. Identify the layers (typically presentation, business, data).
2. For each layer, decide its responsibility in business terms.
3. Decide the dependency direction (always downward).
4. Place each existing or planned class in the appropriate layer.
5. Only then discuss frameworks, ORMs, transports, and concrete technologies.

## Hard rules

### The dependency direction

Dependencies flow **downward** only:

- Presentation → Business → Data.
- Presentation does not call Data directly.
- Business does not call Presentation.
- Data does not call Business or Presentation.

A call that violates the direction is a layered violation.

### One responsibility per layer

- **Presentation** handles input/output: HTTP, CLI, UI rendering, request/response shaping. No business rules.
- **Business** (or Service) holds the rules and orchestration. It does not know about transports or storage details.
- **Data** (or Persistence, DAO, Repository) handles storage: SQL, NoSQL, file IO. It does not know about the business or the transport.

A class that mixes layer responsibilities (a controller that contains business rules; a business class that issues SQL queries directly) is a layered violation.

### No layer skipping

Presentation calls Business. Business calls Data. Presentation does NOT call Data, even when "it would be simpler".

## Forbidden shortcuts

Do not say:

- "Layered is just three folders."
- "We can call the database from the controller, it is the same thing."
- "Business layer can return database rows because they are convenient."
- "Layered means MVC."
- "Layered is legacy by definition."

If the design starts from "what folders to create" rather than from "what each layer does", stop and reframe.

## Mandatory review behavior

When reviewing an existing layered architecture, check in order:

1. Are the layers clearly identified, and does each have one responsibility?
2. Do dependencies flow downward only?
3. Does the presentation layer skip the business layer to reach data?
4. Does the business layer leak transport types (HTTP request/response) or storage types (SQL rows, ORM entities) upward or downward?
5. Are framework concerns confined to the presentation and data layers, kept out of business?
6. Is the data layer leaking types into the business layer (business code typed in terms of database rows)?

## When to pick layered

Layered is a reasonable choice when:

- The domain is **moderately complex**, not rich. Forms over tables with some validation rules and basic workflows.
- The lifetime is **mid-term** (months to a couple of years), not multi-year.
- The team is **small**, single context.
- The framework already imposes a layered shape and the team accepts it.
- The cost of hexagonal/clean/onion ceremony is not justified by the project's complexity.

## When NOT to pick layered

Avoid layered when:

- The domain is **rich**: many invariants, lifecycle that matters, vocabulary owned by the business. Layered's data-shaped models leak upward and erode the domain over time.
- The lifetime is **long**: multi-year projects on layered tend to migrate to hexagonal/clean as the data layer's coupling becomes painful.
- The system is **multi-team / multi-context**: layered does not provide context boundaries.
- The system needs **multiple driving mechanisms** (HTTP, CLI, queue, batch) calling the same logic. Layered's presentation layer is typically tied to one transport.

## Pattern boundary

Layered is independent of:

- Design methods (DDD can run on layered, with weaker boundaries).
- Patterns (CQRS, ES, Repository, Outbox can apply within layered).
- Paradigms (functional, OO).
- Deployment (layered can be a monolith or distributed).

Layered does NOT compose with hexagonal, clean, or onion. They are siblings; pick one.

## References

Load these when needed:

- `references/01-foundations.md` for what layered is, its history, when it applies, when it does not.
- `references/02-the-three-layers.md` for presentation, business, and data layer responsibilities.
- `references/03-dependency-direction.md` for the downward rule and how it differs from hexagonal/clean/onion.
- `references/04-variants.md` for n-tier, integration layer, service layer, and other variants.
- `references/05-anti-patterns.md` for the recurring failures (skipping layers, leaking types, fat business layer).
- `references/06-relationship-with-other-disciplines.md` for layered vs hexagonal/clean/onion/MVC, vs DDD, vs CQRS, vs frameworks.
- `references/07-review-checklist.md` when reviewing an existing layered codebase.
- `references/08-user-preferences.md` for Francisco's project conventions.

Important: do not load hexagonal, clean, or onion skills as part of layered work. They are sibling architectures with different vocabularies. If the case crosses into one, finish the layered step first or migrate (`software-architecture/references/05-migration-paths.md`).
