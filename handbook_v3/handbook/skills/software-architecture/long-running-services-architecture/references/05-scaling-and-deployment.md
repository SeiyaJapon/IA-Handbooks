# 05. Scaling and Deployment

## Horizontal scaling

Adding more process instances. Most common scaling strategy.

Requires:

- The service is **stateless** (or state is externalised).
- Load balancing routes requests across instances.
- Auto-scaling configuration (scale up under load, scale down when idle).

If the service holds state in process memory (sessions, in-memory queues), horizontal scaling requires sticky sessions or moving state out.

## Vertical scaling

Bigger instance: more CPU, more memory.

Used when:

- The workload genuinely needs more per-instance resources (large data sets in memory).
- Horizontal scaling is not feasible (legacy reasons, sticky state).

Limited by the largest available instance type.

## Auto-scaling

Add or remove instances based on metrics:

- CPU utilisation.
- Memory utilisation.
- Queue depth (for queue consumers).
- Request rate (for HTTP services).

Configuration:

- **Cooldown.** Minimum time between scale events; avoids flapping.
- **Health check grace period.** Time for a new instance to become ready.
- **Scale-in protection.** Avoid scaling down during in-flight work.

## Sticky sessions

When a service holds session state in memory, requests for one user must reach the same instance.

Problems:

- A single instance handles a disproportionate load if a user is heavy.
- An instance restart loses sessions.
- Hard to scale.

Mitigations:

- Move sessions to Redis or a shared store.
- Use signed JWTs for stateless authentication.

Sticky sessions are an anti-pattern in modern long-running services unless the workload genuinely requires them (rare).

## Deployment strategies

### Rolling deployment

Replace instances gradually. New version comes up; old version drains; cycle.

Default for most platforms.

Concerns: graceful shutdown matters; readiness probes prevent traffic to instances not yet warm.

### Blue-green deployment

Two environments. Switch traffic from blue to green. Old environment kept warm for fast rollback.

Concerns: cost (two environments). Schema changes must be backward-compatible.

### Canary deployment

Send a small percentage of traffic to the new version. Monitor. Increase gradually.

Concerns: requires routing infrastructure. Detecting issues in canary requires good observability.

## Deployment safety

A safe deployment:

- Graceful shutdown drains in-flight work.
- Readiness probe ensures new instances ready before receiving traffic.
- Rollback is possible (previous version available, schema compatible).
- Monitoring detects issues quickly.

A deploy that causes outages every other release indicates one of these is missing.

## Anti-patterns

- **Deploys cause outages.** Graceful shutdown not implemented; readiness probe wrong; sessions lost.
- **Auto-scaling that flaps.** Scales up, scales down, scales up. Cooldowns too short.
- **No scale-in protection.** Instances killed mid-task.
- **Sticky sessions to avoid moving state out.** State should live outside; sticky sessions hide the problem.
- **No rollback plan.** A bad deploy is permanent until forward-fix.
- **Schema changes without backward compatibility.** Two-step deploys impossible.

## Output

For each long-running service, the team can answer:

- Is it stateless (or state externalised)?
- Horizontal or vertical scaling?
- Auto-scaling metrics and thresholds?
- Deployment strategy?
- Rollback plan?
