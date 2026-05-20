# Node.js Review

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
