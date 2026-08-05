# Serverless Architecture Skill

Use this skill when designing, reviewing, or refactoring software using serverless. Triggers: serverless, Lambda, Cloud Functions, FaaS, function as a service, cold starts, vendor-managed compute.

## What serverless is

Serverless is an architecture and runtime model where **code runs in short-lived, managed compute** triggered by events (HTTP requests, queue messages, schedules, file uploads). The platform owns the runtime; the developer owns the function.

Key examples: AWS Lambda, Google Cloud Functions, Azure Functions, Cloudflare Workers, Vercel Functions.

The defining properties:

- **Short-lived execution.** Each invocation is independent; no long-running process.
- **Vendor-managed runtime.** The platform handles scaling, infrastructure, OS, runtime version.
- **Event-driven.** Functions are triggered by events.
- **Pay per use.** Cost scales with invocations, not with reserved capacity.

## What serverless is and is not

Serverless is an **architecture and runtime model**. It is not:

- A synonym of "stateless". Functions can have state (in databases, in caches); they just do not own the runtime.
- A synonym of "Lambda". Lambda is one platform; serverless is the architectural pattern.
- A replacement for all backends. Long-running services, stateful workloads, latency-critical paths often need long-running compute.
- An automatic best practice. Serverless has costs (cold starts, vendor lock-in, debugging complexity).

## Non-negotiable rule

Never start serverless work from "let's use Lambda".

The mandatory order is:

1. Identify the workloads. What runs? When? How long? How often?
2. Match each workload against serverless fit (event-driven, short, bursty) vs long-running fit (always-on, stateful, latency-critical).
3. Pick serverless only for workloads that fit.
4. Decide the function granularity (one function per use case, one per event type, etc.).
5. Decide cold start mitigation strategy.
6. Decide stateful concerns (database connections, caches, secrets).
7. Only then discuss specific platforms (Lambda, Cloud Functions) and frameworks.

## Hard rules

### Each function has one job

A function does one thing. Composing many operations in one function turns serverless into "monolith on FaaS".

### Functions are stateless

Function state lives outside the function: databases, caches, queues. The function instance may die between invocations.

### Cold starts are real

The first invocation of a function (after idle, after deploy) is slower. Latency-critical paths must mitigate (provisioned concurrency, minimum instances, runtime choice).

### Vendor lock-in is real

Each platform has its own invocation contract, event types, environment limits. Code coupled to the platform is hard to migrate. Mitigate: hexagonal core inside the function, the function handler as a driving adapter.

## Forbidden shortcuts

- "Lambda is the architecture."
- "Each function is a service, so we are doing microservices."
- "Cold starts do not matter."
- "Serverless = stateless = simple."
- "We use Lambda, so we have hexagonal architecture."

## Mandatory review behavior

When reviewing serverless, check in order:

1. Is each workload's serverless fit justified?
2. Does each function have one clear responsibility?
3. Is the function handler a thin driving adapter, with the core inside the function (or in a shared library)?
4. Are cold starts mitigated where they matter?
5. Are connections (database, HTTP) managed correctly across invocations (pooling, connection limits)?
6. Is observability in place (CloudWatch, distributed tracing)?
7. Is the platform coupling minimised, or has the function become a vendor-specific blob?

## When to pick serverless

- Bursty or unpredictable load.
- Event-driven workflows (queue consumers, scheduled jobs, HTTP endpoints with low constant load).
- Operational simplicity matters more than runtime control.
- Cost should scale with usage, not with reserved capacity.

## When NOT to pick serverless

- Long-running processes.
- Latency-critical paths sensitive to cold starts.
- Stateful workloads where state lives in the runtime.
- Complex local state or large memory footprints.
- Workloads where vendor lock-in is unacceptable.

## References

- `references/01-foundations.md` for what serverless is, when it applies.
- `references/02-function-design.md` for granularity, single-responsibility, statelessness.
- `references/03-cold-starts-and-performance.md` for cold start causes and mitigations.
- `references/04-state-and-connections.md` for managing databases, caches, secrets across invocations.
- `references/05-relationship-with-other-disciplines.md` for serverless vs hexagonal/clean/onion (composable internally), vs microservices (composable), vs EDA (natural fit).
- `references/06-anti-patterns.md` for monolith on FaaS, distributed monolith of functions, vendor blobs.
- `references/07-review-checklist.md` for reviewing a serverless system.
- `references/08-user-preferences.md` for Francisco's project conventions.
