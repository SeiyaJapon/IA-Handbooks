# 03. Cold Starts and Performance

## What is a cold start

When a function is invoked and no warm instance is available, the platform creates a new one: download the code, start the runtime, initialise module-level code, then run the handler. This first invocation is the cold start.

Subsequent invocations on the same instance reuse the runtime and module-level state: warm starts. Warm starts are fast.

## What causes a cold start

- **First invocation after deploy.** All instances are new.
- **Idle timeout.** After a period without invocations, the platform retires instances.
- **Scaling up.** A new instance is needed to handle a burst.
- **Configuration changes.** Some changes invalidate warm instances.

## Why cold starts matter

For latency-sensitive paths (synchronous HTTP, user-facing APIs), the cold start latency adds to the response time. On Lambda, cold starts range from ~100ms (Node.js, small bundle) to several seconds (Java, large bundles).

For async paths (queue consumers, scheduled jobs), cold starts usually do not matter.

## Mitigations

### Provisioned concurrency

The platform keeps a number of warm instances ready. New invocations land on warm instances first, hitting cold starts only beyond the provisioned count.

- Pro: predictable latency.
- Con: cost (you pay for the provisioned instances).

### Minimum instances

Some platforms (Cloud Run, Cloud Functions) allow setting a minimum instance count. Same trade-off as provisioned concurrency.

### Runtime choice

Some runtimes start faster than others. Node.js, Python, Go: hundreds of ms. Java with full JVM: seconds. .NET: variable.

For latency-critical paths, choose a fast-starting runtime.

### Bundle size

Smaller bundles deploy faster and start faster. Strip dependencies. Use bundlers (esbuild, webpack) to tree-shake. Avoid large libraries when small alternatives exist.

### Module-level initialisation

What runs at module load determines cold start time. Lazy-load expensive things; only do at module level what is reused on every invocation (database clients, caches, secrets).

### Warm-up pings

Some systems schedule periodic pings to keep instances warm. Brittle: the platform's scheduler may not match invocation patterns. Provisioned concurrency is more reliable.

### Avoid cold-start-sensitive paths

If a path is latency-critical, evaluate whether serverless is the right fit. Long-running compute (EC2, ECS, containers) eliminates cold starts entirely.

## Memory and CPU

Most serverless platforms scale CPU with memory configuration. More memory = faster CPU. For CPU-bound workloads, allocating more memory is often the cheapest performance improvement.

## Concurrency

Each function instance handles one invocation at a time (Lambda, by default). For high concurrency, the platform spins up more instances. Scaling is per-function.

Concurrent connections to downstream systems (databases) scale with the number of warm instances. Database connection limits become a serverless concern (see `04-state-and-connections.md`).

## Anti-patterns

- **Heavy initialisation at module level.** Loading large libraries, opening many connections, fetching secrets synchronously. Cold start times balloon.
- **Latency-critical path with no mitigation.** A user-facing API that times out under load because cold starts add seconds.
- **Provisioned concurrency without measurement.** Provisioning more than needed wastes money.
- **Java for short, latency-sensitive functions without optimisation.** Cold start measured in seconds. Use a JVM-friendly runtime (GraalVM native, AOT) or pick another language.

## Output

For each function, the team can answer:

- Is this path latency-sensitive?
- What is the measured cold start time?
- What mitigations are in place?
- Is the runtime appropriate for the workload?
