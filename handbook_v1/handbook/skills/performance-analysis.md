# Performance Analysis

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