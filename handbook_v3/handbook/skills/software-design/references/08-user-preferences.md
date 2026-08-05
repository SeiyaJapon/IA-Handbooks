# 08. User Preferences (Francisco)

Project conventions for software design across Francisco Pérez's projects at Kintai.

## Default design method

For backend services with rich domains: **DDD**. See `software-design/ddd/`.

For thin services or auxiliary tools: transaction script or framework-default.

## Default principles

- **SOLID** as the baseline. Violations have to be justified.
- **Code readability** as a first-class concern. Names are intentful; functions are short; comments are minimal.
- **Composition over inheritance.** Inheritance is rare and limited.
- **DI through constructors.** No framework decorators in domain or application layers (see hexagonal-architecture and ddd skills).

## Pattern policy

- Patterns are applied when they reduce complexity, not as ceremony.
- The team learns patterns deliberately; patterns the team does not know are not applied yet.
- Pattern over-application is a code review concern.

## Refactoring policy

- Refactoring is part of design discipline, not a separate phase.
- Smells (god classes, fat methods, primitive obsession, long parameter lists) are addressed when noticed.
- Refactor in small steps with tests; do not mix with feature work in the same commit.

## Naming

- PascalCase for classes and types.
- camelCase for variables and functions.
- Domain names from the ubiquitous language (see ddd skill).
- Patterns named after intent (`PriceCalculator`), not implementation (`PriceCalculatorImpl`).

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the project standardises on functional or OO style for utility code (utilities, helpers).
- Whether immutability is enforced through readonly / freeze / library helpers, or by convention only.
- Whether the project uses any automated checks for design smells (linters with cyclomatic complexity, file size, etc.).

These are open. Do not assume; ask.
