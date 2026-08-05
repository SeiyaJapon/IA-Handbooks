# 01. Foundations

## Origin

Serverless as we know it today emerged with AWS Lambda (2014). The concept of vendor-managed event-driven compute predates Lambda (Google App Engine, 2008), but Lambda made it mainstream and cemented the term. Other platforms followed: Google Cloud Functions, Azure Functions, Cloudflare Workers, Vercel/Netlify Functions, etc.

The name "serverless" is a misnomer: there are servers, the platform manages them. A more accurate name would be "Functions as a Service" (FaaS), which is one of the categories under the broader "serverless" umbrella (which also includes managed databases, queues, etc.).

## What serverless solves

- **Operational overhead.** No servers to provision, patch, scale. The platform handles it.
- **Bursty workloads.** The platform scales from zero to many in seconds.
- **Pay per use.** No idle compute cost.
- **Event-driven workflows.** Many serverless platforms have native integrations with event sources (queues, HTTP, schedule, file uploads).

## What serverless does not solve

- **Long-running processes.** Functions have time limits (15 min for Lambda).
- **Latency-sensitive endpoints.** Cold starts add latency.
- **Stateful workloads.** Function instances are short-lived; state lives elsewhere.
- **Complex local state.** Memory limits, no persistent disk.
- **Vendor lock-in.** Each platform has its own invocation contract, event types, runtime versions.

## When serverless applies

- Bursty or unpredictable load.
- Event-driven workflows.
- Workloads with low constant load.
- Tasks that finish in seconds or minutes, not hours.
- Teams that value operational simplicity.

## When serverless does not apply

- Long-running processes.
- Latency-critical paths where cold start budget is tight.
- Workloads with large memory or persistent disk.
- Workloads that require runtime control (specific OS, custom kernel, GPUs in some contexts).
- Workloads where vendor lock-in is unacceptable.

## Common misreadings

- **"Serverless is the modern way."** Not always. Long-running services are still the right choice for many backends.
- **"Lambda = microservices."** Lambdas can be functions of a microservice or independent functions. Microservices is about deployment independence; serverless is about runtime model.
- **"Cold starts are solved."** Mitigated, not solved. Provisioned concurrency, minimum instances, runtime choice all reduce cold starts but do not eliminate them.

## Decision

Ask:

1. Is the workload bursty or event-driven?
2. Can it complete within the platform's time limit?
3. Is cold start latency acceptable for this path?
4. Is vendor lock-in acceptable, or mitigated?

Two no's: serverless is not the right fit. Use long-running compute or another model.
