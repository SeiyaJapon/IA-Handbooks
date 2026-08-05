# Subprocess Management Skill

## When to use

The user asked about spawning child processes, signal handling, stream capture (stdout/stderr), timeouts on subprocesses, PID lifecycle, zombie processes.

## When not to use

- General concurrency: `concurrency-patterns`.
- Long-running services: `long-running-services-architecture`.
- Container processes (Docker): `docker-review`.

## Inputs to inspect first

- The subprocess library/API in use.
- Signal handling: SIGTERM, SIGINT, SIGCHLD.
- Stream capture: pipes, buffer limits, line-buffering vs block.
- Timeouts and kill strategy.
- PID and process group lifecycle.

## How to work

1. Identify how the subprocess is spawned.
2. Walk concerns: timeouts, signal propagation, stream capture, zombie processes, environment isolation.
3. Recommend minimal changes.

## Output

Findings tied to specific spawn sites and process lifecycle hooks.

## Escalation

- Long-running daemon shape: `long-running-services-architecture`.
- Container build issues: `docker-review`.

---

## Purpose

Gestionar procesos hijos de forma segura: timeouts, captura de streams, señales, limpieza al cancelar. Un subprocess mal gestionado puede colgarse indefinidamente, dejar zombies o corromper el estado del padre.

## Responsibilities

- Lanzar procesos con timeout obligatorio
- Capturar stdout y stderr sin bloquear por buffer lleno
- Propagar señales del padre al hijo (`SIGINT`, `SIGTERM`)
- Garantizar limpieza al cancelar (kill del hijo, drenado de pipes)
- Interpretar exit codes y distinguir éxito de fallo transitorio o permanente
- Aislar el entorno del hijo (variables, cwd, file descriptors)
- Detectar comandos que requieren TTY o input interactivo y manejarlos correctamente

## Instructions

- Comprueba: ¿hay un timeout explícito en cada lanzamiento? Sin timeout es bug latente
- Comprueba: ¿se leen stdout y stderr concurrentemente o se acumulan hasta deadlock?
- Comprueba: ¿qué pasa si el padre recibe `Ctrl+C` mientras el hijo corre?
- Comprueba: ¿el hijo queda huérfano si el padre muere abruptamente?
- Comprueba: ¿hay logging del comando exacto que se lanzó (incluyendo args y env relevante)?
- Comprueba: ¿los exit codes se interpretan o se tratan todos como "error genérico"?
- Comprueba: ¿se reusa el entorno del padre o se construye uno explícito y mínimo?

## Heuristics

Treat as stronger concerns when:

- `subprocess.run(..., timeout=None)` o equivalente sin límite
- Captura síncrona de un solo stream y el otro queda colgado
- No hay manejo de `KeyboardInterrupt` o `SIGTERM` en el padre
- El comando se construye por concatenación de strings (riesgo de injection)
- Logs sin el comando real ejecutado, sólo con "error al ejecutar X"
- Procesos largos sin streaming de output (el usuario no sabe si avanza)
- Hijo que necesita TTY ejecutado sin pty (`expect`-style) y se cuelga esperando input

Treat as acceptable when:

- Comandos triviales con runtime conocido y predecible (ej. `git rev-parse`)
- Scripts de uso único donde el coste de robustez supera el riesgo

## Rules

- Todo subprocess tiene timeout configurable y un default conservador
- Comando se pasa como lista de argumentos, nunca como string concatenado con shell
- Streams capturados de forma no-bloqueante (threads, asyncio o `Popen` con polling)
- El padre instala handlers de señal antes de lanzar hijos
- Logging mínimo: comando exacto, exit code, duración, primeras N líneas de stderr
- Hijos huérfanos prohibidos: usar process groups o `subreaper` cuando aplique

## Activity Traceability

🔧 Loading skill: `subprocess-management`
