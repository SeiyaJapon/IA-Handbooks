# 03. Resource Management

A long-running service holds resources for its entire lifetime. Bounded resources are non-negotiable; unbounded resources are slow-motion crashes.

## Memory

The process has a memory limit (container limit, JVM heap, Node `--max-old-space-size`). Hitting it causes OOM kill (in containers) or out-of-memory errors.

Sources of memory growth:

- **Caches without eviction.** Grow with unique keys.
- **In-memory accumulation.** Logs, metrics, request history kept in memory.
- **Large response objects retained.** A response held by a long-running operation.
- **Memory leaks.** References that should be released, are not.

Mitigations:

- Bounded caches (LRU, TTL).
- Stream processing instead of loading collections into memory.
- Profiling under load.
- Memory metrics and alerts.

## CPU

CPU usage spikes can starve other operations or trigger throttling.

Sources:

- Synchronous expensive computations on the request path.
- Blocking operations in async runtimes (Node event loop blocked).
- Large GC pauses (JVM, .NET).

Mitigations:

- Move expensive work to background workers.
- Avoid blocking the event loop in single-threaded runtimes.
- Tune GC.
- CPU metrics and alerts.

## Connection pools

Database connections, HTTP client connections, broker connections are pooled. Each pool has a maximum size.

Concerns:

- **Pool exhaustion.** All connections in use; new requests block or fail.
- **Connection leaks.** Connections not returned to the pool. Pool drains over time.
- **Pool sized wrong.** Too small (bottleneck) or too large (downstream overwhelmed).

Mitigations:

- Pool size matched to workload concurrency and downstream limits.
- Connection lifecycle in code: acquire, use, release in `finally`.
- Pool metrics: idle, active, waiting.

## File descriptors

Sockets, files, pipes consume FDs. The OS limits per process.

Concerns:

- FD leaks (sockets not closed). Process hits limit; new connections fail.
- Many concurrent connections in network-heavy services.

Mitigations:

- Close FDs explicitly.
- Increase ulimit if the workload genuinely needs more.
- Monitor FD usage.

## Threads / event loop

Different runtimes, different concerns:

- **Single-threaded async runtimes (Node, Python asyncio):** event loop must not block. Blocking calls move to worker threads.
- **Multi-threaded runtimes (JVM, .NET, Go):** thread pools sized; long blocking work uses dedicated pools.
- **Goroutines / fibers:** lighter, but still have limits.

A blocked event loop in Node means the entire process is unresponsive.

## Disk

Most long-running services do not need persistent disk. Logs go to stdout (collected by the platform). Caches go to Redis or external systems.

When disk is needed:

- Bounded usage (logs rotated, temp files cleaned).
- Disk metrics and alerts.

## Backpressure

When a service receives more work than it can handle:

- HTTP: return 503 or 429 instead of accepting and timing out.
- Queue: stop polling instead of accepting messages it cannot process.
- Workers: signal the producer to slow down.

Without backpressure, the service crashes under load.

## Anti-patterns

- **Unbounded cache.** Memory grows until OOM.
- **Pool size = 1.** Only one concurrent operation.
- **Pool size unbounded.** Downstream overwhelmed.
- **Connection leak.** Pool drains.
- **Blocking operation on the event loop.** Process unresponsive.
- **No metrics.** Resource exhaustion is silent until crash.
- **No backpressure.** Crashes under load instead of rejecting cleanly.

## Output

For each long-running service, the team can answer:

- Memory limit and usage profile?
- CPU usage profile?
- Connection pool sizes and metrics?
- FD usage?
- Backpressure strategy?
