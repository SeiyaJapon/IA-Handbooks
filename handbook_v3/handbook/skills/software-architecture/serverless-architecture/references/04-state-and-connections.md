# 04. State and Connections

## State lives outside

Function instances are short-lived. State that survives across invocations lives in:

- Databases.
- Caches (Redis, Memcached).
- Queues.
- Object storage.
- External APIs.

The function reads and writes state; it does not store it.

## Module-level state within an instance

Code that runs at module load is reused across invocations on the same instance. This is the place for:

- Database clients (with connection pooling).
- HTTP clients with keep-alive.
- Configuration loaded from environment.
- Secrets fetched at startup.

```typescript
// Module level: runs once per instance.
const dbClient = createDbClient(process.env.DATABASE_URL);

export const handler = async (event) => {
  // Reuses dbClient.
};
```

What does NOT belong at module level:

- Per-invocation state.
- Anything that depends on the event being handled.
- Heavy initialisation that delays cold start.

## Database connections

Serverless functions and traditional databases (Postgres, MySQL) have a famous mismatch: traditional databases handle a fixed number of concurrent connections; serverless can scale to many concurrent function instances, each opening its own connection.

Mitigations:

- **Connection pooling at module level.** Each instance reuses one pool across invocations.
- **Database proxy.** AWS RDS Proxy, PgBouncer, etc., absorb the spike of connections from many functions.
- **Connection-less databases.** DynamoDB, Cosmos DB, Firestore use HTTPS per request; no connection pool concern.
- **Limit concurrent function executions.** Cap the number of instances a function can have to limit downstream pressure.

A function that opens a new database connection per invocation, without pooling and without a proxy, will exhaust the database's connection limit under load.

## Secrets

Secrets (API keys, database passwords) live outside the function code. Options:

- Environment variables, set by the platform from a secret store (AWS Secrets Manager, Parameter Store).
- Fetched from a secret store at module load (cached in memory for the instance lifetime).
- Fetched per invocation when freshness matters (rare).

Avoid hardcoding secrets in the function bundle.

## Configuration

Configuration that does not change per invocation is loaded at module level (environment, parameter store).

Configuration that changes (feature flags, runtime settings) is read per invocation, with appropriate caching to avoid hammering the source.

## Caches

Caching across function invocations:

- **Within an instance:** module-level variables. Fast, but invalidation across instances is hard.
- **Cross-instance:** external cache (Redis, Memcached). Slower per access but consistent.
- **Edge cache:** CDN (CloudFront, Cloudflare). For HTTP responses.

The wrong shape: a "cache" that is a module-level Map that the team assumes works across all function instances. It does not; each instance has its own.

## Stateful workflows

When a workflow spans multiple function invocations, the state must live somewhere visible to all of them:

- **Step Functions / Durable Functions.** Platform-managed workflow state.
- **Database row.** A workflow record updated as steps complete.
- **Saga pattern.** Events drive state transitions; each event's handler is a function.

In-memory state across functions is not a thing.

## Output

For each function, the team can answer:

- What state does the function need across invocations?
- Where does that state live?
- Are connections pooled at module level?
- Are secrets handled correctly?
- Is there a cache strategy, and is it consistent across instances?

Without these, the function works in tests and fails in production.
