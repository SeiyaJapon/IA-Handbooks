# TypeScript Review

## Purpose

Review TypeScript code for type safety, idiomatic patterns, and common pitfalls that undermine the value of the type system.

## Responsibilities

- Assess type coverage and appropriate use of the type system
- Detect `any` usage that defeats type safety
- Review generic type usage for correctness and readability
- Assess null and undefined handling
- Evaluate union and discriminated union design
- Review type assertion usage (`as`, `!`)
- Assess module structure and export conventions
- Detect implicit type widening and narrowing issues

## Instructions

- Check for `any` usage: every `any` should have a documented reason or be replaced
- Check for non-null assertions (`!`): each one is a promise to the compiler that can fail at runtime
- Check type assertions (`as`): are they safe? is there a guard that justifies them?
- Check for `unknown` vs `any` — prefer `unknown` when the type is truly unknown
- Check for optional chaining vs type guards — are null cases handled intentionally?
- Check discriminated unions: is the discriminant field used consistently?
- Check generics: are constraints applied? is the generic actually needed?
- Check `strict` mode: is it enabled? are `strictNullChecks` and `noImplicitAny` on?

## Heuristics

Treat as stronger concerns when:

- `any` used to work around type errors instead of fixing the type
- Non-null assertion (`!`) on values that can genuinely be null/undefined
- Type assertions (`as`) without a preceding type guard
- `strict: false` in `tsconfig.json`
- Return types omitted on public functions — implicit `any` risk
- `object` or `{}` used instead of a specific interface or type alias
- Untyped function parameters in callbacks passed to typed APIs

Treat as acceptable when:

- `any` in genuinely dynamic boundaries (e.g. JSON from external APIs before parsing)
- Non-null assertion where the null case is provably impossible and a comment explains why

## Rules

- `strict: true` is required — never disable strict mode to silence errors
- Every `any` requires a comment explaining why the type cannot be specified
- Non-null assertions must be justified — they are runtime failure points
- Return types must be declared on all exported functions

## Activity Traceability

🔧 Loading skill: `typescript-review`
