# Node.js Review Skill

## When to use

The user asked for a Node.js review (event loop, streams, child processes, async patterns, package conventions, runtime behaviour) of a file, module, or PR.

## When not to use

- TypeScript-specific concerns (types, generics, strictness): `typescript-review`.
- Backend architecture or layering: `software-architecture`.
- Long-running services / queue consumers / graceful shutdown at the architecture level: `long-running-services-architecture`.
- Serverless / Lambda runtime concerns: `serverless-architecture`.
- Testing strategy: `testing-strategy`.

## Inputs to inspect first

- `package.json` (scripts, engines, dependencies, type field), lockfile, Node version constraint (`.nvmrc`, engines).
- Module format (ESM vs CommonJS), top-level await usage, `"type": "module"`.
- Test runner and lint setup.
- Process model: HTTP server, CLI, worker, Lambda. Runtime model dictates conventions.
- Streams, async iterators, event emitters in use.

## How to work

1. Inspect the project configuration before judging the code.
2. Identify the runtime model (server, CLI, Lambda, worker).
3. Run `npm test`, `npm run lint`, `npm run typecheck` if practical and safe.
4. Walk Node-specific concerns (event-loop blocking, unhandled rejections, stream backpressure, child process lifecycle, signals, graceful shutdown).
5. Group findings by severity. Do not modify code unless asked.

## Output

Findings cited by file and line, grouped: blockers (event-loop blocking, unhandled rejections, leaks), defects (stream backpressure, missing graceful shutdown, weak error contexts), nits (style, ESM/CJS consistency).

## Escalation

- Architecture issues: `software-architecture` and the relevant sub-skill.
- Long-running service operational concerns: `long-running-services-architecture`.
- Lambda specifics: `serverless-architecture`.
- TypeScript concerns: `typescript-review`.
- Subprocess/signal handling design: `subprocess-management`.
- Errors beyond Node idiom: `error-handling-patterns`.
- Security: `security-review`.

---

## Purpose

Review Node.js code for correctness, idiomatic patterns, and pitfalls specific to the runtime: event loop, async model, streams, and module system.

## Responsibilities

- Review async/await and Promise usage for correctness
- Detect event loop blocking patterns
- Evaluate stream usage for large data handling
- Review error propagation in async contexts
- Assess module structure and circular dependency risks
- Review EventEmitter usage and listener cleanup
- Evaluate process lifecycle and signal handling
- Detect memory leak patterns specific to Node.js

## Instructions

- Check async functions: are all Promises awaited or explicitly handled?
- Check for unhandled promise rejections — every `.catch()` or try/catch in async must be present
- Check for CPU-bound operations on the main thread — they block all I/O
- Check streams: are `data`, `end`, and `error` events all handled?
- Check EventEmitter: are listeners removed when no longer needed? (`removeListener`, `once`)
- Check `require` cycles — circular dependencies cause partially-initialized modules
- Check process.exit usage — is cleanup (DB connections, in-flight requests) done before exit?
- Check for `setInterval` or open handles that prevent process exit

## Heuristics

Treat as stronger concerns when:

- Synchronous I/O (`fs.readFileSync`, `JSON.parse` on large payloads) in request handlers
- `Promise` created but not awaited and not stored — fire-and-forget without error handling
- Unhandled rejection in async code path (missing try/catch or `.catch`)
- EventEmitter listeners added in a loop or per-request without removal — memory leak
- Circular `require` — hard to diagnose, causes undefined imports
- `process.exit()` without closing DB connections or finishing in-flight work
- Synchronous crypto or file operations in hot paths

Treat as acceptable when:

- `fs.readFileSync` at module load time (not per-request)
- `process.exit(1)` in CLI scripts where cleanup is irrelevant

## Rules

- Every Promise in an async path must be awaited or explicitly handled
- CPU-bound work belongs in a worker thread, not the main thread
- EventEmitter listeners added dynamically must be removed on cleanup
- Unhandled rejection is never acceptable in production code

## Activity Traceability

🔧 Loading skill: `node-review`
