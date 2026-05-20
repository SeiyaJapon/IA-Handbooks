# Long-Running Services

## Purpose

Review the design of persistent, long-running services (containerized APIs, workers, daemons) for correctness, resilience, resource management, and operational readiness.

## Responsibilities

- Evaluate process lifecycle and graceful shutdown design
- Assess connection pool configuration and resource limits
- Review background worker and queue consumer design
- Evaluate health check and readiness probe implementation
- Assess memory and CPU profile for the expected load
- Review restart and recovery strategy
- Evaluate state management for in-flight requests during restart
- Assess horizontal scalability and session affinity requirements

## Instructions

- Check graceful shutdown: does the process drain in-flight requests before exiting? is SIGTERM handled?
- Check connection pools: are database and HTTP client pools sized for concurrency? are idle timeouts configured?
- Check health checks: is there a `/health` or `/ready` endpoint? does it reflect actual readiness (DB connected, dependencies up)?
- Check background workers: do they acknowledge jobs only after successful processing? is there a dead-letter path?
- Check memory: are there unbounded caches or growing in-memory collections?
- Check horizontal scaling: is the service stateless, or does sticky session routing apply?
- Check restart strategy: does a crash-loop cause data loss or duplicate processing?

## Heuristics

Treat as stronger concerns when:

- No SIGTERM handler — process killed mid-request causing incomplete transactions
- Database connection pool without a timeout or maximum size
- Background worker acknowledging jobs before processing (at-most-once delivery)
- Health endpoint returning 200 without checking actual dependencies
- In-memory state that is lost on restart and not recoverable
- Unbounded in-memory cache with no eviction policy
- Service with session affinity in a horizontally scaled deployment without explicit documentation

Treat as acceptable when:

- Simple stateless API without background workers or complex shutdown logic
- Missing readiness probe in development environments

## Rules

- Graceful shutdown is required: drain in-flight work before exiting
- Health checks must reflect actual readiness, not just process liveness
- Background workers must guarantee at-least-once delivery with idempotent processing
- Connection pools must have bounded size and timeout configuration

## Activity Traceability

🔧 Loading skill: `long-running-services`
