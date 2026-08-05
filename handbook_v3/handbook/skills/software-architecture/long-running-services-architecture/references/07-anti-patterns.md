# 07. Anti-patterns

## No graceful shutdown

The service does not handle SIGTERM. Every deploy kills in-flight requests.

Fix: SIGTERM handler that drains in-flight, closes pools, exits.

## Liveness = readiness

One probe used for both. Either traffic reaches not-ready services, or services restart when downstreams are slow.

Fix: separate probes. Liveness checks process; readiness checks dependencies.

## Unbounded cache

In-memory cache grows with unique keys. Eventually OOM.

Fix: LRU or TTL eviction. Bounded size.

## Connection pool sized wrong

Too small: bottleneck. Too large: downstream overwhelmed.

Fix: size matched to workload concurrency and downstream limits. Monitor pool usage.

## Connection leak

Connections acquired and not released. Pool drains over time.

Fix: lifecycle in code (acquire, use, release in `finally`).

## Blocking the event loop

In single-threaded async runtimes (Node, Python asyncio), a blocking call freezes the process.

Fix: move blocking work to worker threads or workers. Profile under load.

## In-process state assumed durable

Sessions, queues, caches in process memory. Restart loses them.

Fix: state outside the process (Redis, database, queue).

## Sticky sessions to hide stateful design

Routing requests to a specific instance because state lives there.

Fix: move state out. Stateless services scale and recover better.

## Deployments cause outages

Every deploy drops requests, fails health checks, or causes errors.

Fix: graceful shutdown + correct readiness probe + rolling deploys with grace period.

## Crash loops with no alert

The process crashes repeatedly. The platform restarts it. Nobody notices.

Fix: alerts on restart count.

## Auto-scaling that flaps

Scales up, scales down, scales up. Cooldowns too short or thresholds too tight.

Fix: cooldowns sized to deployment time; thresholds with hysteresis.

## "Containers solve it"

The team containerised and assumed all the operational concerns are solved. They are not.

Fix: containers are packaging. The operational concerns (lifecycle, resources, probes, scaling, deploys) are still the team's.

## No metrics

Memory, CPU, pool usage, request rate, latency. Without them, problems are invisible until crash.

Fix: instrument. Dashboard. Alert.

## Output

When reviewing long-running services, look for these patterns. No graceful shutdown, liveness=readiness, unbounded resources are the most common.
