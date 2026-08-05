# 02. Process Lifecycle

A long-running service has a lifecycle: start, ready, run, shutdown, restart. Each phase has design implications.

## Start

The process boots. What happens:

- Configuration loaded from environment, secrets manager, config service.
- Connection pools opened (database, Redis, brokers).
- Caches warmed (if applicable).
- Background workers started.
- HTTP server bound to port (but not yet accepting traffic from the load balancer).

Bad start sequence: open connections before validating config; bind ports before downstream is reachable.

## Ready

The process signals it is ready to handle traffic. The load balancer adds it to the pool.

What "ready" means:

- Configuration is valid.
- Connections to required downstreams are healthy.
- Caches are warm (if warm cache is required).
- The service can handle the workload.

What "ready" does NOT mean:

- "The process started." That is liveness, not readiness.
- "The HTTP server bound to a port." That is process state, not service state.

## Run

The service handles requests, processes messages, runs workers. Steady state.

## Graceful shutdown

The platform sends SIGTERM (Unix) or platform-equivalent. The service must:

1. Stop accepting new requests / messages.
2. Drain in-flight requests / messages within a timeout.
3. Close connections cleanly.
4. Exit with code 0.

If the timeout expires, the platform sends SIGKILL and the process dies abruptly.

A service that does not handle SIGTERM loses in-flight work on every deploy.

## Implementation

```
process.on("SIGTERM", async () => {
  log.info("SIGTERM received, draining...");
  server.close();              // Stop accepting new connections.
  await drainInFlight();       // Wait for current requests.
  await closePools();          // Close database, broker connections.
  process.exit(0);
});
```

For queue consumers: stop polling, finish current message, ack, exit.

For workers: stop accepting tasks, finish current task, exit.

## Restart

When the process crashes or is killed, the platform restarts it.

Concerns:

- **Idempotent startup.** Restart must not corrupt state.
- **No data loss.** In-flight work must be retried (recover from queue, re-fetch task).
- **No duplicate processing.** Idempotent consumers prevent double-processing on retry.
- **Crash loops.** A repeatedly crashing service must alert; the platform stops restarting after N attempts.

## Cold start vs warm

Long-running services have one cold start (at process start) and warm performance afterwards. This is the operational benefit over serverless.

Cold start time matters for deployment speed and recovery from crashes. A 30-second cold start means 30 seconds of unavailability per crash.

## Anti-patterns

- **No SIGTERM handling.** In-flight requests dropped on every deploy.
- **Readiness = liveness.** The service receives traffic before it can handle it.
- **Synchronous startup that takes minutes.** Slow deploys, slow recovery.
- **No timeout on shutdown.** Hangs forever if a request never completes.
- **State that survives in process memory only.** Restart loses it; no recovery.
- **No alerting on crash loops.** The service crashes silently.

## Output

For each long-running service, the team can answer:

- What happens at start?
- When does the service become ready?
- How does it handle SIGTERM?
- What is the shutdown timeout?
- What happens on crash and restart?
- Is restart safe (no data loss, no duplicates)?
