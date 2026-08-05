# Performance Analysis Skill

## When to use

The user asked about latency, throughput, scaling, bottlenecks, profiling, or specific performance regressions.

## When not to use

- Generic "make it faster" without a concrete target or measurement: ask for the SLO, the profile, or a baseline first.
- AI-specific cost/latency: `llm-ops`.
- Database performance specifically: `database-design`.

## Inputs to inspect first

- The performance target (SLO, latency budget, throughput target).
- The current measurement (profile, traces, metrics).
- The bottleneck candidate (CPU, memory, I/O, lock contention, GC, network).
- The workload shape (sustained, bursty, asymmetric).

## How to work

1. Verify there is a measurement; otherwise refuse to optimise blind.
2. Identify the dominant cost.
3. Recommend the smallest change that improves the dominant cost.
4. Confirm with re-measurement.

## Output

Findings with measured baseline, identified bottleneck, recommended change, expected delta.

## Escalation

- Database queries and indexes: `database-design`.
- AI cost/latency: `llm-ops`.
- Architectural scaling: `software-architecture` and the relevant sub-skill (microservices, serverless, long-running).

---

## Purpose

Evaluate performance, scalability, latency, and cost concerns in a technical case.

## Responsibilities

- Identify possible performance bottlenecks
- Evaluate latency, throughput, scalability, and resource usage
- Detect inefficient data access or excessive network calls
- Consider operational cost, especially in cloud/serverless systems
- Identify whether performance concerns are real, likely, or speculative
- Recommend measurement or optimization direction when useful

## Instructions

- Start from the expected behavior and load when available
- Distinguish measured problems from hypothetical concerns
- Identify hot paths, repeated operations, and external calls
- Check database access, queries, batching, pagination, and indexing when relevant
- Check messaging, concurrency, retries, and backpressure when relevant
- Consider cloud cost and cold starts when relevant
- Prefer measurement before optimization when evidence is weak

## Heuristics

Treat as stronger performance concerns when:

- The case affects high-volume paths
- It introduces loops with database or network calls
- It adds synchronous calls to external services
- It processes large datasets without batching or pagination
- It touches queues, lambdas, retries, or scheduled jobs
- It increases payload size, serialization cost, or memory usage
- It may increase cloud cost significantly

Treat as lower concern when:

- The path is low-volume
- The change is local and not in a hot path
- Data size is bounded
- Existing performance is measured and acceptable
- Optimization would add complexity without clear benefit

## Rules

- Do not optimize without evidence or a plausible risk signal
- Prefer measurement before complex optimization
- Make performance trade-offs explicit
- Consider operational cost, not only latency
- Avoid premature complexity
- Recommend simple mitigations first

## Activity Traceability

🔧 Loading skill: `performance-analysis`