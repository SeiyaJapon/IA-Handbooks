# Long-Running Services Architecture Skill

Use this skill when designing, reviewing, or operating long-running services: persistent processes, containerised APIs, daemons, queue consumers, schedulers running as services. Triggers: long-running services, daemons, persistent processes, containers, ECS, Kubernetes pods, EC2 services, queue workers, graceful shutdown.

## What long-running services architecture is

An architecture and runtime model where the service runs as a **persistent process**: started once, kept alive, handles many requests or messages over its lifetime, restarts on failure or deploy.

Defining properties:

- **Persistent process.** Lifetime measured in hours, days, weeks.
- **Owns its runtime.** State in memory, connection pools, caches, in-flight requests.
- **No cold starts after start.** Constant warm performance.
- **Operational responsibility.** Health, restart, scaling, monitoring are first-order concerns.

## What it is and is not

Long-running is the alternative runtime model to **serverless**. Both deploy services; the difference is who owns the runtime.

It is NOT:

- A specific architecture inside the service. The service can be hexagonal, clean, onion, layered internally.
- A synonym of "monolith". A long-running service can be one microservice; a monolith can be long-running.
- A synonym of "container". Containers are one packaging; long-running services predate containers.

## Non-negotiable rule

Never start long-running work from "let's spin up a container".

The mandatory order is:

1. Identify the workload type (HTTP API, queue consumer, scheduler, worker, daemon).
2. Decide why long-running fits (vs serverless): always-on, latency-critical, stateful, long operations.
3. Decide the lifecycle: start, ready, shutdown, restart.
4. Decide resource limits: memory, CPU, connections, file descriptors.
5. Decide health and readiness signals.
6. Decide scaling strategy: horizontal, vertical, auto-scaling.
7. Decide deployment strategy: rolling, blue-green, canary.
8. Only then discuss specific platforms (ECS, Kubernetes, EC2).

## Hard rules

### Graceful shutdown

The service handles SIGTERM (or platform equivalent) and drains in-flight work before exiting. New requests rejected; existing requests finished; connections closed cleanly.

A service that loses in-flight work on shutdown corrupts state under normal deploys.

### Bounded resources

Memory, CPU, connection pools, caches, queues. None unbounded. Caches with eviction. Pools with maximum sizes. Queues with backpressure.

An unbounded resource is a slow-motion crash.

### Health and readiness

Two distinct signals:

- **Liveness:** is the process alive? If not, restart it.
- **Readiness:** is the process ready to handle traffic? If not, the load balancer holds traffic back.

A service that is alive but not ready (still warming up, lost a downstream) should not receive traffic.

### State outside the process

Process state (in-memory caches, sessions, in-flight work) does not survive a restart. State that must survive lives in databases, caches, queues.

## Forbidden shortcuts

- "Containerised = production-ready."
- "Auto-scaling solves all scaling needs."
- "Health checks = ping endpoint."
- "We will add graceful shutdown later."
- "Restart is fine; restart fixes everything."

## Mandatory review behavior

When reviewing a long-running service:

1. Is graceful shutdown implemented (SIGTERM handled, requests drained)?
2. Are connection pools and resource limits configured?
3. Are health and readiness probes correct (not just "alive")?
4. Is process state minimal? Does state outside the process survive restart?
5. Is restart safe? Does a crash-loop cause data loss or duplicate processing?
6. Is horizontal scaling possible? Or is the service stateful in a way that prevents it?
7. Is the deployment strategy safe (rolling, blue-green, canary)?

## When to pick long-running

- The service handles continuous load.
- Latency-sensitive paths where serverless cold starts do not fit.
- Stateful workloads (caches, in-memory queues, long-lived connections).
- Long-running operations that exceed serverless time limits.
- Specific runtime control (custom OS, GPUs, large memory).

## When NOT to pick long-running

- Bursty or unpredictable load with low average rate (serverless is cheaper).
- Simple event-driven handlers with no state.
- Operational simplicity is the priority.

## References

- `references/01-foundations.md` for what long-running services are, when they apply.
- `references/02-process-lifecycle.md` for start, ready, graceful shutdown, restart.
- `references/03-resource-management.md` for memory, CPU, connections, pools.
- `references/04-health-and-readiness.md` for probes and load balancer integration.
- `references/05-scaling-and-deployment.md` for horizontal scaling, rolling deploys, canary.
- `references/06-relationship-with-other-disciplines.md` for long-running vs serverless, vs hexagonal/clean/onion (composable internally).
- `references/07-anti-patterns.md` for unbounded caches, no graceful shutdown, alive-not-ready.
- `references/08-review-checklist.md` for reviewing a long-running service.
- `references/09-user-preferences.md` for Francisco's project conventions.
