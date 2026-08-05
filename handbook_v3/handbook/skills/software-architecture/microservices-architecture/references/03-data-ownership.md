# 03. Data Ownership

Each service owns its data. This is not a guideline; it is the rule that distinguishes microservices from a distributed monolith.

## The rule

- Each service has its own datastore (database, schema, namespace).
- No service reads or writes another service's data directly.
- Cross-service data access goes through the owning service's API or events.

Violations of this rule are the most common reason microservices migrations fail.

## Why

If two services share a database:

- Schema changes in one service break the other.
- The two services cannot be deployed independently (a schema change is a coordinated release).
- Failure isolation is broken (a database problem affects both services).
- The boundary is a fiction; the database is the real boundary, and it is shared.

## What "owns" means

- The service decides the schema.
- The service decides the migrations.
- The service is the only one that connects to the database with read-write credentials.
- Other services that need the data go through the service's API or subscribe to its events.

Read-only access from a reporting tool is acceptable as a separate concern (operational read access), not as an architecture pattern.

## Cross-service data access patterns

When service A needs data owned by service B:

### Synchronous query

A calls B's API: `GET /products/{id}`. Used when:

- The data must be fresh.
- The interaction is occasional.
- The latency cost is acceptable.

Risks: latency stacking, cascading failure if B is slow.

### Asynchronous event consumption

B publishes events when its state changes. A subscribes and maintains its own copy of the relevant data (a read model, a projection).

Used when:

- Eventual consistency is acceptable.
- The interaction is frequent (avoid the per-request latency).
- Decoupling is valuable (A keeps working when B is down).

### Materialised views

A reporting service consumes events from many services and builds aggregated views. The views are owned by the reporting service; the source services are unaware.

### Anti-corruption layer (ACL)

When A consumes events from B, A translates B's vocabulary into its own at the boundary. This isolates A from B's internal model changes.

## Anti-patterns in data ownership

### Shared database

The most common failure. Two or more services connect to the same database.

Why it fails: deployment coupling, schema coupling, no failure isolation. The system is a monolith with extra HTTP.

Fix: split the database. This is often the hardest part of microservices migration.

### Cross-service reads bypassing the owner

Service A queries B's database directly with read-only credentials.

Why it fails: A depends on B's schema. Schema changes break A.

Fix: A queries B's API or subscribes to B's events.

### Distributed transactions

Service A and B commit a single database transaction across both. Two-phase commit.

Why it fails: brittle, slow, hard to reason about. Most modern systems avoid distributed transactions.

Fix: sagas. Each service commits its own transaction; events propagate the change. Compensation handles failures.

### Write fan-out

Service A writes to B's database when it should be calling B's API.

Why it fails: same as shared database. A knows B's schema.

Fix: call B's API.

## Operational concerns

Each service's database is a separate operational unit:

- Separate backup.
- Separate scaling.
- Separate access control.
- Separate compliance scope.

This is operational cost. It is real and measurable. It is one of the reasons microservices is not free.

## Output

For each service, the team can answer:

- What data does this service own?
- Where is it stored?
- How do other services access it (API, events)?
- Are there any cross-service direct database accesses?

Cross-service direct database accesses are findings to fix.
