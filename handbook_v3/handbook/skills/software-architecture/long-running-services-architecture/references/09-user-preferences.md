# 09. User Preferences (Francisco)

Project conventions for long-running services in Francisco Pérez's projects at Kintai.

## When long-running is acceptable

Long-running is the default for:

- HTTP APIs with continuous load.
- Queue consumers (SQS-based workers; the term `consumer` is canonical for these).
- Workers performing operations that exceed serverless time limits.
- Stateful services (rare; usually deferred to managed alternatives like Redis).

Serverless is the alternative for bursty event-driven workloads.

## Default platforms

- **AWS ECS** for containerised long-running services.
- **AWS EC2** when ECS does not fit.
- **Docker** as the canonical container format.

## Internal architecture

Each long-running service is hexagonal internally by default. The internal architecture is decoupled from the runtime model.

## Lifecycle

- SIGTERM handler in every service. Drains in-flight requests, closes pools, exits.
- Shutdown timeout matches the platform's grace period.
- Startup is bounded; alerts if startup exceeds an expected threshold.
- Crash loops trigger alerts.

## Resources

- Memory and CPU limits configured in the deployment manifest.
- Connection pools sized per service, with metrics.
- Caches with eviction (LRU + TTL).
- Backpressure: HTTP returns 503 / 429; consumers stop polling when overloaded.

## Health probes

- `/health` (liveness): cheap, process-only check.
- `/ready` (readiness): checks required downstream dependencies.
- Both wired to the platform's health configuration.

## Scaling

- Services are stateless by default (state in Redis, database, queue).
- Horizontal scaling with auto-scaling policies based on CPU, memory, and queue depth.
- Cooldowns sized to deployment time.

## Deployment

- Rolling deploys with grace period.
- Schema changes are backward-compatible across one release.
- Rollback path tested.

## Observability

- Metrics published to the central monitoring system.
- Logs structured, with correlation IDs.
- Distributed tracing instrumented.
- Alerts: crash loops, memory pressure, pool exhaustion, error rates.

## Communication style

Avoid em dash in normal prose. Prefer parentheses or separate sentences. Only keep em dash when quoting existing text or explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether long-running services in this project default to ECS Fargate, ECS EC2, or pure EC2 with Docker.
- Whether sticky sessions are ever allowed (default: no).
- Whether deploys default to rolling, blue-green, or canary.
- Whether the term `consumer` extends to workers that are not SQS-based (e.g. Kafka consumers).

These are open. Do not assume; ask.
