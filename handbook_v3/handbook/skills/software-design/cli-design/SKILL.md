# CLI Design Skill

## When to use

The user asked about command-line interface design: subcommands, flags, positional arguments, exit codes, help, output format, progress reporting.

## When not to use

- The CLI is a thin wrapper around an HTTP API: `api-design` for the underlying contract.
- The CLI's internals are software design: `software-design`.

## Inputs to inspect first

- CLI library in use (argparse, Click, Typer, oclif, commander, cobra).
- Existing subcommands and conventions.
- Exit code policy.
- Help and version output.

## How to work

1. Inspect the existing CLI surface.
2. Walk concerns: subcommand grouping, flag consistency, positional vs optional, exit codes, help quality, output for humans vs scripts.
3. Recommend minimal changes.

## Output

Findings tied to subcommand or flag.

## Escalation

- Underlying API contract: `api-design`.
- CLI as a driving adapter: relevant architecture skill.

---

## Purpose

Estructurar herramientas de línea de comandos para que sean descubribles, predecibles y seguras. Una buena CLI deja claro qué hace sin abrir el código y nunca sorprende al usuario con efectos no deseados.

## Responsibilities

- Diseñar jerarquía de subcomandos coherente con el dominio
- Definir flags con nombres consistentes (`--apply`, `--dry-run`, `--verbose`)
- Asignar exit codes con significado (0 éxito, distintos no-cero por familia de error)
- Garantizar que `--help` y la salida sin args son auto-explicativos
- Distinguir output legible (TTY) de output máquina (`--json`)
- Detectar acciones destructivas sin gating explícito
- Verificar comportamiento ante señales (`SIGINT`, `SIGTERM`) y entrada cancelada

## Instructions

- Comprueba: ejecutar sin args o con `--help` ¿explica lo esencial?
- Comprueba: los subcomandos agrupan funcionalidad de forma natural (`tool run`, `tool list`, `tool doctor`)
- Comprueba: las acciones irreversibles requieren flag explícito (`--apply`, `--force`)
- Comprueba: el exit code distingue éxito, error de usuario, error interno y interrupción
- Comprueba: la salida cambia cuando se redirige a pipe (sin colores, sin spinners)
- Comprueba: los mensajes de error dicen qué pasó y qué hacer, no sólo "Error"
- Comprueba: no hay efectos secundarios en `--help` ni en commands de lectura

## Heuristics

Treat as stronger concerns when:

- El comportamiento por defecto es destructivo y `--dry-run` es opt-in
- `--help` es un muro de texto sin ejemplos
- Exit code es siempre 0 o 1 sin distinguir tipos de fallo
- La CLI mezcla output legible y datos máquina por el mismo stdout
- Acciones lentas sin progreso ni feedback al usuario
- Flags con nombres ambiguos (`-f` puede ser `--force` o `--file`)
- Cambios silenciosos en flags entre versiones (rotura de scripts que la usan)

Treat as acceptable when:

- Scripts internos one-shot sin necesidad de subcomandos
- Output JSON puro en herramientas pensadas para pipelining

## Rules

- Toda CLI tiene `--help` autocontenido y al menos un ejemplo
- Acciones destructivas requieren flag explícito, nunca por defecto
- Exit codes siguen una tabla documentada; nunca son aleatorios
- Output a stderr para mensajes operativos, stdout para resultados
- `SIGINT` cierra limpiamente: sin estado corrupto y con mensaje al usuario

## Activity Traceability

🔧 Loading skill: `cli-design`
