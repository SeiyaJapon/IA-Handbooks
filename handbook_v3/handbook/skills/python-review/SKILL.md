# Python Review

## Purpose

Review Python code for correctness, idiomatic style, and common pitfalls — particularly in the context of Lambda functions, data scripts, and automation.

## Responsibilities

- Review code structure, module organization, and entry points
- Detect common Python anti-patterns and language-specific traps
- Assess error handling and exception specificity
- Evaluate type annotation coverage on public interfaces
- Review dependency management (requirements files, virtual environments, pinning)
- Assess performance for data processing patterns
- Review async usage for correctness
- Detect Lambda-specific issues (warm invocation state, init code placement)

## Instructions

- Start from the entry point (handler, `main`, script)
- Check exception handling: are exceptions specific? are they logged? are they swallowed silently?
- Check for mutable default arguments (`def f(x=[])` — a classic Python trap)
- Check for `import *` in non-`__init__.py` files
- Check for global state that persists across Lambda warm invocations
- Check blocking I/O (`requests.get`, `time.sleep`) inside `async` functions
- Check type annotations on public interfaces and function signatures
- Check dependency pinning in requirements files used in production
- Check `__init__.py` placement and module import paths

## Heuristics

Treat as stronger concerns when:

- Bare `except:` or `except Exception:` without logging or re-raise
- Mutable default arguments (`[]`, `{}`, or any object as default)
- `import *` outside `__init__.py`
- Unpinned or loosely pinned dependencies in production requirements
- Blocking I/O calls inside `async def` functions
- Global variable mutation in Lambda handlers (state persists across warm invocations)
- Missing type annotations on public functions or Lambda handlers
- Catching and discarding exceptions (`except: pass`)

Treat as acceptable when:

- Missing type annotations on internal private helpers
- Loosely pinned dependencies in dev or test requirements files

## Rules

- Exception handling must be specific and must log — catch-and-discard is never acceptable
- Lambda handlers must treat global state as potentially warm
- Mutable default arguments are never acceptable
- Production requirements must pin all dependencies

## Activity Traceability

🔧 Loading skill: `python-review`
