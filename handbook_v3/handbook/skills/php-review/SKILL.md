# PHP Review

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
