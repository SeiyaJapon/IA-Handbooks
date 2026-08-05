# PHP Review Skill

## When to use

The user asked for a PHP review (idiomatic style, type declarations, framework usage, dependency hygiene) of a file, module, or PR.

## When not to use

- Architecture: `software-architecture`.
- Design beyond PHP idiom: `software-design`.
- API contract design: `api-design`.
- Errors beyond PHP idiom: `error-handling-patterns`.
- Testing strategy: `testing-strategy`.

## Inputs to inspect first

- `composer.json`, `composer.lock`, PHP version, autoload (PSR-4) configuration.
- Framework: Laravel, Symfony, classical layered. Conventions differ.
- Lint/static analysis: PHPStan, Psalm, PHP CS Fixer; phpunit/pest config.
- `.env` and config layout; environment-specific overrides.
- Strict types (`declare(strict_types=1)`) usage and pattern.

## How to work

1. Inspect `composer.json` and framework conventions.
2. Run `composer test`, `composer phpstan`, `composer cs` if practical.
3. Walk PHP-specific concerns: strict types, type declarations on parameters/returns, nullability, exception hierarchy, dependency injection container usage, immutability where applicable, late static binding, generators for memory.
4. Group findings by severity. Do not modify code unless asked.

## Output

Findings cited by file and line, grouped: blockers (untyped public API on framework-bound code, swallowed exceptions, SQL injection vectors), defects (weak null handling, framework-coupling in domain code, missing strict types), nits (style, naming).

## Escalation

- Architecture: `software-architecture`.
- Design beyond PHP idiom: `software-design`.
- Errors beyond PHP idiom: `error-handling-patterns`.
- API contracts: `api-design`.
- Security: `security-review`.

---

## Purpose

Review PHP code for correctness, security, idiomatic modern style, and common language-specific pitfalls.

## Responsibilities

- Review use of modern PHP features (8.x: typed properties, match, fibers, named args)
- Assess error and exception handling
- Detect security vulnerabilities specific to PHP (SQL injection, XSS, file inclusion)
- Review type safety: strict types, return types, parameter types
- Evaluate dependency management via Composer
- Assess framework-specific conventions (Laravel, Symfony, etc.) when applicable
- Review session and authentication handling
- Detect use of deprecated or unsafe functions

## Instructions

- Check for `declare(strict_types=1)` at the top of files
- Check type declarations on all functions and class methods
- Check for SQL query construction via string concatenation — must be parameterized
- Check for `eval()`, `system()`, `exec()`, `shell_exec()` — flag all usage
- Check for user-controlled file paths (path traversal risk)
- Check `$_GET`/`$_POST`/`$_REQUEST` — is input validated and sanitized before use?
- Check output: is user-controlled content escaped before rendering (`htmlspecialchars`)?
- Check error reporting: is `display_errors` off in production?

## Heuristics

Treat as stronger concerns when:

- SQL queries built via string concatenation with user input
- `eval()` or dynamic code execution with user-controlled input
- User-controlled input used in `include`/`require` paths
- Missing `strict_types=1` in new files
- Raw `$_GET`/`$_POST` used without validation
- `display_errors = On` in production configuration
- Deprecated functions (`mysql_*`, `ereg_*`, `split()`)
- Missing return type declarations on public methods

Treat as acceptable when:

- Missing strict types in legacy files not being refactored
- Missing type hints on private internal helpers in older codebases

## Rules

- Parameterized queries are non-negotiable
- User input must be validated before use and escaped before output
- Dynamic code execution with user input is never acceptable
- `display_errors` must be off in production

## Activity Traceability

🔧 Loading skill: `php-review`
