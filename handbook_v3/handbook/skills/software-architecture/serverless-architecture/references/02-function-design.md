# 02. Function Design

## Granularity

A function does one thing. Granularity options:

- **Function per use case.** Each business operation is a function. Easy to reason about, clear responsibility.
- **Function per event type.** A function handles all events of a type (all SQS messages from queue X). Common in event-driven workflows.
- **Function per HTTP route.** Each HTTP endpoint is a function. Common in serverless APIs.
- **Function per workflow step.** A long workflow split across functions, with state in a queue or step function.

What to avoid:

- **One function per service.** A "PaymentService" function that handles every payment operation. The function has many jobs; cold starts are larger; failures affect everything.
- **One mega-function for the whole API.** Returns the team to a monolith without the benefits of long-running compute.

## Function structure

A function has two parts:

1. **The handler.** The platform-specific entry point (`exports.handler = (event, context) => {...}` in Lambda). Receives the platform's invocation contract.
2. **The core.** The actual logic. Should be free of platform coupling.

Treat the handler as a **driving adapter** in hexagonal vocabulary. It translates the platform's event into a call on the application core. The core is the same code that could run in any other context.

```typescript
// handler.ts (driving adapter)
import { useCase } from "./application/createScenarioUseCase";

export const handler = async (event: APIGatewayEvent) => {
  const command = parseCommand(event);
  const result = await useCase.execute(command);
  return formatResponse(result);
};
```

## Handler responsibilities

- Parse the platform's event into a domain-meaningful input.
- Call the application core.
- Translate the result into the platform's expected response format.
- Map exceptions to appropriate platform-specific responses.

Non-responsibilities:

- Business rules.
- Direct database access (the use case calls a repository through a port).
- Multi-step orchestration (orchestration belongs in the use case or in a step function / saga).

## Statelessness

Each function instance is potentially short-lived. State that survives across invocations must live outside the function:

- Databases (RDS, DynamoDB, Postgres).
- Caches (Redis, Memcached, ElastiCache).
- Queues (SQS, SNS).
- Object storage (S3).

In-memory state inside the function is allowed for the duration of a single invocation. Across invocations, it cannot be relied on (the instance may die or be replaced).

## Module-level reuse

Within one function instance, module-level code (executed once at load) can be reused across multiple invocations. This is where database connections and clients live:

```typescript
const dbClient = new DatabaseClient(...);  // Module level: created once per instance.

export const handler = async (event) => {
  // Reuses dbClient across invocations on the same instance.
};
```

This is **the** trick that makes serverless functions efficient with databases. See `04-state-and-connections.md` for details.

## One use case, one function (recommended default)

For HTTP and event-triggered functions, the recommended default is one function per use case:

- One function per HTTP endpoint (or per logical operation).
- One function per event type.
- Shared code (the application core) lives in a shared library imported by each function.

This keeps cold starts smaller, responsibilities clear, and observability per-operation.

## Anti-patterns

- **Mega-handler.** A single function that handles many operations through internal routing.
- **Handler with business rules.** Decisions in the handler instead of the core.
- **Handler that reaches a database directly without a port.** Couples the use case to platform and database.
- **State in module variables across invocations.** "Caches" that are unreliable because the instance may be cold.
- **Long-running work in a function.** Hits the time limit, fails partway.

## Output

For each function, the team can answer:

- What is its one job?
- Is the handler thin?
- Is the core platform-free?
- What state lives outside the function?
