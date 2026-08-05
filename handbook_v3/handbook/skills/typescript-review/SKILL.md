# TypeScript Review Skill

Review TypeScript code for **type safety, idiomatic patterns, and common pitfalls** that undermine the value of the type system. Operational: inspect the project before judging the code.

## When to use

Use this skill when:

- The user asks for a TypeScript review of a file, module, or PR.
- A code review surfaces `any`, non-null assertions, unsafe casts, missing return types.
- A team is debating strictness, module format, or framework conventions.
- A specific TypeScript feature (generics, discriminated unions, conditional types) is being reviewed.

## When not to use

Do not use this skill when:

- The question is **architectural** (where modules live, how layers depend, hexagonal/clean/onion). Use `software-architecture` and the relevant sub-skill.
- The question is **about design at the function/class level beyond types** (SOLID, patterns, DI). Use `software-design`.
- The question is **about a Node.js runtime concern** (event loop, streams, child processes). Use `node-review`.
- The question is **about React/Vue/Angular component design**. Use `frontend-architecture`.
- The question is **about errors** beyond type representation. Use `error-handling-patterns`.

## Inputs to inspect first

Before judging TypeScript code, inspect the project's configuration. Without this context, advice is generic and often wrong.

- **`tsconfig.json`** (and any `tsconfig.*.json` variants): `strict`, `target`, `module`, `moduleResolution`, `lib`, `paths`, `noEmit`, `isolatedModules`, `verbatimModuleSyntax`. Strict-mode level changes what counts as a violation.
- **`package.json`**: package manager (look at the lockfile too), scripts (`build`, `test`, `lint`, `format`, `typecheck`), engines, dependencies vs devDependencies, framework conventions (Next, NestJS, Express, Vite).
- **Lockfile**: `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`. Confirms the package manager.
- **Test setup**: Jest, Vitest, Mocha, configuration file, test discovery, framework.
- **Lint setup**: ESLint config, TypeScript-specific plugins, project rules.
- **Formatter**: Prettier, dprint, presence of `.editorconfig`. Do not fight the formatter.
- **Module format**: ESM, CommonJS, mixed. Affects imports, extensions in import paths, conditional exports.
- **Path aliases**: `paths` in tsconfig and any alias plugin (`tsconfig-paths`, `tsc-alias`, bundler resolution).
- **Build tooling**: `tsc`, `esbuild`, `swc`, `vite`, `webpack`, `tsup`. Affects what is emitted and how.
- **Runtime environment**: Node version, browser, Bun, Deno, Workers. Affects available APIs.
- **Code kind**: production, test, generated, scripts, migrations, prototypes, boundary code.

If any of these is unclear, the review starts there. Without project context, the advice is generic.

## Hard rules (about agent behaviour)

- **Inspect project configuration before judging code.**
- **Inspect nearby files before reviewing a file in isolation.** Conventions are local.
- **Do not modify code unless the user asked for changes.** Reviews are reviews; edits are edits.
- **If modifying code, keep the change minimal and aligned with the active skill.** Do not refactor a file because you happen to be there.
- **Run typecheck/test/lint when available and practical.** `npx tsc --noEmit`, `npm test`, `npm run lint` if present and the user expects validation.
- **If commands cannot be run, say so explicitly.** Do not assume.
- **Group findings by severity or decision impact.** Critical issues first; nits last.
- **Avoid large refactors when the user asked for a focused review.**
- **Escalate when evidence shows the issue crosses TypeScript-only boundaries.**

## TypeScript-specific concerns

- **`any` usage.** Every `any` should be deliberate, documented, and at a true dynamic boundary.
- **Non-null assertions (`!`).** Each is a runtime failure point. Justify or replace with a guard.
- **Type assertions (`as`).** Should follow a guard or be unavoidable; bare `as` is a smell.
- **`unknown` vs `any`.** Prefer `unknown` for genuinely unknown data; force callers to narrow.
- **Optional chaining vs guards.** Optional chaining hides intent; guards make null cases explicit.
- **Discriminated unions.** The discriminant must be consistent across the codebase.
- **Generics.** Constraints applied; generics actually needed (not added "in case").
- **Return types.** Exported functions declare return types explicitly.
- **`object` and `{}` as types.** Almost never the right type; replace with specific.
- **Untyped callbacks.** Inferred but not enforced; tighten when passed across module boundaries.
- **Type-only imports.** `import type { X }` to avoid runtime cost when appropriate.
- **`strict: true`.** Required. If disabled, that is the first finding.
- **Module format consistency.** ESM vs CommonJS divergence at boundaries causes subtle bugs.

## How to work

1. **Read the project configuration** (`tsconfig.json`, `package.json`, lint setup).
2. **Identify the code kind** (production, test, generated, script).
3. **Run the project's typecheck/test/lint** if available and practical.
4. **Read the file in context.** Look at neighbours; conventions are local.
5. **Walk the TypeScript-specific concerns** above, ordered by severity.
6. **Group findings**: blockers, defects, nits, suggestions.
7. **Suggest minimal changes** aligned with the project's conventions.
8. **Escalate** when evidence shows the issue crosses TypeScript-only boundaries.

## Output

Return findings as:

- **Blockers:** failures of the type system that produce runtime risk (`any` to silence errors, unsafe casts, non-null on nullable, missing return types on public API).
- **Defects:** correctness or maintainability issues that should be fixed (inconsistent discriminated unions, leaky generics, `object` as type, optional chaining hiding nulls).
- **Nits:** stylistic preferences not blocking (organisation, naming, type-only imports).
- **Suggestions:** opportunities for cleaner types when scope allows.

Cite file paths and line numbers. Reference project configuration when the violation depends on it.

## Escalation

- The review surfaces an **architectural** issue (module boundary, dependency direction, layer leak): load `software-architecture` and the relevant sub-skill.
- The review surfaces a **design** issue (SOLID, DI, patterns): load `software-design` or the relevant sub-skill.
- The review surfaces a **Node runtime** concern (event loop, streams): load `node-review`.
- The review surfaces a **React/Vue/Angular** concern: load `frontend-architecture`.
- The review surfaces an **error handling** concern beyond type representation: load `error-handling-patterns`.
- The review surfaces a **test** concern: load `testing-strategy`.
- The review surfaces a **security** concern: load `security-review`.

A TypeScript review that escalates does not stretch into the neighbouring concern; it routes there.

## Anti-patterns the agent should reject

- **Refactoring without being asked.**
- **Demanding `strict: true` without verifying the project's tsconfig and migration cost.**
- **Demanding a specific module format without inspecting the runtime.**
- **Suggesting framework changes during a focused review.**
- **Generic advice ("write clean code", "consider scalability") not tied to a concrete file or rule.**
