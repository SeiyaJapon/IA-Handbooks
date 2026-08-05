# 04. Health and Readiness

Two distinct signals. Confusing them is a common cause of production incidents.

## Liveness

"Is the process alive?"

- The platform calls a `/health` (or similar) endpoint at intervals.
- A response means the process is alive.
- A failure (or no response) means restart it.

The liveness check should be cheap and not depend on external systems. A liveness check that fails because Postgres is slow causes the process to restart, which does not fix Postgres.

## Readiness

"Is the process ready to handle traffic?"

- The platform calls a `/ready` (or similar) endpoint.
- A success means add the process to the load balancer pool.
- A failure means remove it (but do not restart).

Readiness checks dependencies: can the process reach the database? the broker? the cache?

A process that is alive but not ready (still starting up, downstream temporarily unavailable) should not receive traffic.

## Why two signals

If only one signal exists, the team faces a dilemma:

- Make it strict (check downstreams): the process restarts when downstreams are slow, making things worse.
- Make it lenient (check only process state): traffic reaches a process that cannot serve it.

Two signals allow:

- Liveness lenient: the process restarts only when truly broken.
- Readiness strict: traffic flows only when the process can handle it.

## Implementation

```
GET /health
  Returns 200 if the process can respond. Cheap.

GET /ready
  Returns 200 if all required downstreams are reachable. Verbose.
```

## Startup probes

Some platforms have a third probe:

- **Startup probe:** "is the process still starting?"

While the startup probe is running, liveness and readiness are deferred. Useful for slow-starting services.

## Probes that depend on others

A probe that calls another service for liveness creates cascading checks. Service A's probe calls B, B's calls C, C's calls A. A cycle.

Avoid: liveness checks only the local process. Readiness checks only direct dependencies, not transitive.

## Probe timeouts

Probes have timeouts. Set them realistically; too short causes false negatives.

## Anti-patterns

- **Single endpoint as both probes.** One signal collapses two distinct concerns.
- **Liveness checks downstream.** Restarts cascade when downstream is slow.
- **Readiness identical to liveness.** Traffic reaches not-ready processes.
- **Probe that does heavy work.** Slows the service under high probe rates.
- **Probe that returns 200 unconditionally.** No actual check.
- **No probes.** The platform has no way to detect broken processes.

## Output

For each long-running service, the team can answer:

- What does liveness check?
- What does readiness check?
- Are the probes cheap and fast?
- How does the load balancer integrate?
