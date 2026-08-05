# Python Review Skill

Review Python code for **correctness, idiomatic style, and common pitfalls**: typing, error handling, dependency hygiene, async correctness, runtime model fit. Operational: inspect the project before judging the code.

## When to use

Use this skill when:

- The user asks for a Python review of a file, module, or PR.
- A code review surfaces bare excepts, mutable default arguments, `import *`, unpinned dependencies, missing type annotations, blocking I/O in async code.
- A team is debating typing strictness, dependency management, or testing setup.
- A Lambda handler, data script, ML notebook, or Django/FastAPI module is being reviewed.

## When not to use

Do not use this skill when:

- The question is **architectural** (modules, layers, hexagonal/clean/onion). Use `software-architecture`.
- The question is **about design beyond Python idioms** (SOLID, patterns, DI). Use `software-design`.
- The question is **about a data pipeline** as a whole (ingestion, transformation, scheduling). Use `data-pipeline-architecture`.
- The question is **about classical ML** (features, splits, leakage). Use `ml-review`.
- The question is **about errors** beyond Python idiom. Use `error-handling-patterns`.
- The question is **about testing strategy**. Use `testing-strategy`. This skill checks idiomatic test code; the strategy itself is upstream.

## Inputs to inspect first

Before judging Python code, inspect the project. Without this context, advice is generic.

- **`pyproject.toml`** (preferred) or **`setup.cfg`** / **`setup.py`**: project metadata, build backend, dependencies, optional extras, tool config (ruff, mypy, pytest).
- **Requirement files**: `requirements.txt`, `requirements-*.txt`, `requirements.lock`, `Pipfile.lock`, `poetry.lock`, `uv.lock`. The lockfile reveals the dependency manager.
- **Python version**: `python-version`, `pyproject.toml` `requires-python`, `runtime.txt`, Lambda runtime, Docker base image.
- **Package layout**: src layout vs flat; `__init__.py` placement; namespace packages.
- **Typing setup**: `mypy.ini`, `pyrightconfig.json`, `pyproject.toml` `[tool.mypy]` or `[tool.pyright]`. Strictness level.
- **Test framework**: `pytest`, `unittest`, configuration, fixtures, markers.
- **Lint and formatter**: `ruff`, `black`, `isort`, `flake8`, `pylint`. The active linter dictates conventions.
- **Dependency manager**: `pip`, `poetry`, `pipenv`, `uv`, `hatch`, `pdm`. Affects how to add/remove packages.
- **Runtime model**: synchronous (Flask, Django sync), async (FastAPI, asyncio), Lambda (warm invocations), data/notebook (Jupyter), CLI (Click, Typer).
- **Code kind**: application code, library code, script code, data/ML code (often different conventions).

## Hard rules (about agent behaviour)

- **Inspect project configuration before judging code.**
- **Inspect nearby files before reviewing a file in isolation.** Conventions are local.
- **Do not modify code unless the user asked for changes.**
- **If modifying code, keep the change minimal and aligned with the active skill.**
- **Run the project's lint/typecheck/test when available and practical.** `pytest`, `ruff check`, `mypy`, `pyright` if configured.
- **If commands cannot be run, say so explicitly.**
- **Group findings by severity or decision impact.**
- **Avoid large refactors when the user asked for a focused review.**
- **Distinguish application code from script code from data/ML notebook code.** Conventions and standards differ.

## Python-specific concerns

- **Exception handling.** Bare `except:` and `except Exception:` without logging or re-raise are bugs. Specific exception types or `except: pass` with explicit reason.
- **Mutable default arguments.** `def f(x=[])` is a classic Python trap. Never acceptable.
- **`import *`.** Outside `__init__.py`, almost always wrong.
- **Type annotations.** Public functions, library APIs, Lambda handlers should be annotated. Internal helpers may be loose if the team's typing strictness allows.
- **Async correctness.** Blocking I/O (`requests.get`, `time.sleep`, sync `open`) inside `async def` blocks the event loop. Use the async equivalents.
- **Global mutable state.** In Lambda or long-running workers, globals persist across invocations. Reset or design around it.
- **Dependency pinning.** Production requirements pin exact versions; dev/test may be looser.
- **f-strings vs `.format` vs `%`.** f-strings preferred in modern Python; consistency matters more than the choice.
- **`pathlib` vs `os.path`.** `pathlib` is the modern default.
- **`logging` vs `print`.** Production code uses `logging` configured at the entry point.
- **Context managers.** Files, locks, transactions, network resources use `with` blocks.
- **Generators and iterators.** Avoid materialising large collections in memory unless required.
- **Dataclasses, TypedDict, Protocol.** Modern typing primitives; avoid hand-rolled boilerplate.
- **`Optional[T]` vs `T | None`.** Project's typing version determines which is idiomatic.

## Code-kind-specific guidance

- **Application code.** Strict typing, full error handling, structured logging, dependency injection, test coverage.
- **Library code.** Strict typing on public API; minimal dependencies; semantic versioning.
- **Script code.** Smaller surface; entry point clear; logging instead of print; argparse/Click/Typer for CLI.
- **Lambda handler code.** Treat globals as warm; cold start time relevant; structured logging; minimal dependencies; pinned versions.
- **Data/ML code.** Reproducibility (seeds, data versioning); pipeline-shaped reasoning; less strict on application-level typing; more strict on data shape contracts.

## How to work

1. **Read the project configuration.**
2. **Identify the code kind.**
3. **Run lint/typecheck/test** if available and practical.
4. **Read the file in context.** Conventions are local.
5. **Walk the Python-specific concerns** above, ordered by severity.
6. **Group findings**: blockers, defects, nits, suggestions.
7. **Suggest minimal changes** aligned with the project's conventions.
8. **Escalate** when evidence shows the issue crosses Python-only boundaries.

## Output

Return findings as:

- **Blockers:** correctness bugs, silent exception swallowing, mutable defaults, blocking in async, leaking globals in Lambda.
- **Defects:** missing types on public API, unpinned production dependencies, `import *`, weak error context.
- **Nits:** style preferences not blocking; pathlib vs os.path; f-string consistency.
- **Suggestions:** opportunities for cleaner code when scope allows.

Cite file paths and line numbers. Reference project configuration when the violation depends on it.

## Escalation

- Architecture, layers, modules: `software-architecture`.
- Design beyond Python idiom (SOLID, patterns, DI): `software-design`.
- Errors beyond exception idiom: `error-handling-patterns`.
- Async at architecture level (long-running services, queue consumers): `long-running-services-architecture` or `event-driven-architecture`.
- Data pipelines: `data-pipeline-architecture`.
- Classical ML: `ml-review`.
- Testing strategy (vs idiomatic test code): `testing-strategy`.
- Security: `security-review`.

## Anti-patterns the agent should reject

- Refactoring without being asked.
- Demanding strict typing without inspecting the project's typing setup.
- Demanding a specific dependency manager.
- Suggesting framework changes during a focused review.
- Generic advice not tied to a concrete file or rule.
