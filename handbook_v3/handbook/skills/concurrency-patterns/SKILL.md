# Concurrency Patterns

## Purpose

Review concurrency and parallelism design for correctness, safety, and performance — across async code, workers, queues, and shared state.

## Responsibilities

- Evaluate async/await and Promise composition patterns
- Detect race conditions and non-deterministic execution paths
- Review shared state access and synchronization
- Assess parallel execution strategy (Promise.all, worker threads, queues)
- Review backpressure and concurrency limiting
- Detect deadlock risks
- Evaluate idempotency under concurrent execution
- Review timeout and cancellation design

## Instructions

- Identify where concurrent execution occurs: async functions, workers, event handlers, queue consumers
- Check shared mutable state: is access serialized? is there a risk of read-modify-write races?
- Check `Promise.all` vs `Promise.allSettled`: is partial failure handled correctly?
- Check concurrency limits: can unbounded parallel execution exhaust resources (DB connections, file handles)?
- Check backpressure: if a producer is faster than a consumer, what happens?
- Check cancellation: is there a way to abort long-running concurrent work?
- Check idempotency: if a concurrent operation runs twice, is the outcome correct?
- Check timeouts: does concurrent work have a deadline?

## Heuristics

Treat as stronger concerns when:

- Shared mutable state accessed from multiple async paths without synchronization
- `Promise.all` where one rejection silently aborts all concurrent work without recovery
- Unbounded `Promise.all` over a large array — can exhaust DB connection pool or rate limits
- Fire-and-forget async calls with no error tracking and no cancellation
- Queue consumer with no concurrency limit — can flood downstream services
- Race condition in a check-then-act pattern (`if (!exists) { create() }` without a lock or unique constraint)
- Deadlock risk from nested locks or circular dependencies between async operations

Treat as acceptable when:

- Unbounded parallelism for purely CPU-bound work with a known small dataset
- No cancellation for short-lived operations where the cost is negligible

## Rules

- Shared mutable state across async boundaries requires explicit synchronization
- Unbounded parallelism must be justified — default to a concurrency limit
- Every concurrent operation must have a timeout or a cancellation path
- Race conditions in check-then-act patterns require an atomic operation or a unique constraint at the storage level

## Activity Traceability

🔧 Loading skill: `concurrency-patterns`
