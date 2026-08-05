# 08. Review Checklist

## Step 1: Does long-running fit?

1. Continuous load (vs bursty)?
2. Latency consistency required?
3. In-memory state valuable?
4. Long operations?

## Step 2: Process lifecycle

1. Graceful shutdown implemented (SIGTERM handled)?
2. In-flight work drained on shutdown?
3. Startup is bounded (not minutes)?
4. Restart is safe (no data loss, idempotent)?

## Step 3: Resources

1. Memory bounded? Caches with eviction?
2. Connection pools sized correctly?
3. No connection leaks?
4. Backpressure strategy?

## Step 4: Health and readiness

1. Liveness and readiness as separate probes?
2. Liveness cheap and not dependent on downstreams?
3. Readiness checks required dependencies?
4. Probes integrated with the load balancer?

## Step 5: Scaling

1. Service is stateless (or state externalised)?
2. Horizontal scaling possible?
3. Auto-scaling configured with proper cooldown?
4. Scale-in protection where needed?

## Step 6: Deployment

1. Rolling, blue-green, or canary?
2. Rollback plan?
3. Schema changes backward-compatible during deploys?
4. Health check grace period correct?

## Step 7: Internal architecture

1. Hexagonal / clean / onion / layered? Justified?
2. Domain logic separated from runtime concerns?
3. Tested in isolation?

## Step 8: Observability

1. Metrics: memory, CPU, pool usage, request rate, latency?
2. Logs: structured, correlation IDs?
3. Distributed tracing?
4. Alerts: crash loops, memory pressure, pool exhaustion?

## Summary

Top three findings. Quick wins. Backlog. Confirmed strengths.

## Forbidden conclusions

- "It is fine because it is in a container."
- "It is fine because Kubernetes runs it."
- "It is fine because the health check returns 200."

The properties are lifecycle, resources, probes, scaling, deployment, observability. Container and platform alone do not validate the service.
