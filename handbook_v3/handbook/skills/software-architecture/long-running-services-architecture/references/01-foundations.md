# 01. Foundations

Long-running services are persistent processes that handle continuous workload over time. The model predates serverless and remains the right choice for many backends.

## What it solves

- **Latency consistency.** No cold starts.
- **Stateful workloads.** In-memory caches, sessions, connection pools survive across requests.
- **Long operations.** Operations that take minutes or hours.
- **Runtime control.** Specific OS, kernel, hardware (GPUs, large memory).
- **Cost predictability.** Continuous load is often cheaper than serverless invocations.

## What it does not solve

- Bursty workloads with low average rate. Idle time is paid.
- Operational simplicity. The team owns the runtime.
- Pay-per-invocation cost models.

## When it applies

- HTTP APIs with sustained load.
- Queue consumers processing messages continuously.
- Schedulers running as services.
- Workers performing long-running tasks.
- Stateful services (caches, message brokers, in-memory queues).

## When it does not apply

- Bursty or unpredictable load with low average rate. Serverless is cheaper and simpler.
- Simple event handlers with no continuous load.
- Workloads where vendor-managed compute is acceptable.

## Common platforms

- **Containers:** Docker on ECS, Kubernetes (EKS, GKE, AKS), Cloud Run (long-running mode).
- **Virtual machines:** EC2, Compute Engine, Azure VMs.
- **Bare metal:** when specific hardware is required.
- **Process managers:** systemd, PM2 (for older Node deployments).

The platform is operational; the architecture is the service's design.

## Internal architecture

The internal architecture of a long-running service can be:

- Hexagonal / clean / onion (recommended for rich domains).
- Layered (for moderate-complexity services).
- A monolith (one service handling many features).
- Part of a microservices system (one bounded context per service).

Long-running is the runtime; the internal architecture is a separate decision.

## Decision

Ask:

1. Is the workload continuous (or bursty)?
2. Is latency consistency important?
3. Is in-memory state valuable?
4. Are operations sometimes long?

Two yeses out of four: long-running is a candidate. Compare with serverless to decide.
