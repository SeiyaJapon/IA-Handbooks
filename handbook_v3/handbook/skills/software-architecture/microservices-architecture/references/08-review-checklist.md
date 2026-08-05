# 08. Review Checklist

Operational checklist for reviewing a microservices system, after the cross-architecture review confirmed microservices is in use.

## Step 1: Are the forces satisfied?

1. Multiple teams with independent release cadence?
2. Asymmetric change rates between parts?
3. Independent scaling needs?
4. Operational maturity in place (deploys, monitoring, tracing, on-call)?

**Stop condition.** If two are no, recommend consolidating into a modular monolith.

## Step 2: Are service boundaries coherent?

1. Does each service own one bounded context (or a deliberate variation)?
2. Is each service owned by one team?
3. Is each service coherent in business terms?
4. Are there services that look like CRUD-per-entity, verb-shaped, or frontend-shaped?

## Step 3: Is data ownership respected?

1. Does each service own its data?
2. Is there any shared database, schema, or table between services?
3. Are cross-service data accesses through APIs or events, never through direct database reads?

**Stop condition.** Shared databases invalidate microservices for the affected services. Stop and recommend splitting the database.

## Step 4: Can each service be deployed independently?

1. Can a service be deployed without coordinating with another?
2. Are there releases that require multi-service deploys?
3. Are contract changes managed (versioning, migration windows)?

## Step 5: Is communication well designed?

1. For each pair of communicating services, sync or async, and is the choice justified?
2. Are there synchronous chains of three or more services?
3. Are contracts explicit, documented, versioned?
4. Are consumers idempotent?
5. If sagas are used, are they observable?

## Step 6: Are failure modes handled?

1. Timeouts on every synchronous call?
2. Circuit breakers where appropriate?
3. Retries with backoff?
4. Dead-letter queues for failed messages?
5. Transactional outbox where the system cannot afford to lose events?

## Step 7: Is the internal architecture of each service sound?

1. Is each service hexagonal/clean/onion internally (or layered for moderate services)?
2. Or is each service a small ball of mud?
3. Is there cross-service code duplication that suggests a missing shared library or a missing abstraction?

## Step 8: Is the system observable end-to-end?

1. Distributed tracing?
2. Structured logs aggregated?
3. Metrics per service and end-to-end?
4. Dashboards and alerts?

## Summary

After all steps, produce:

- **Top three findings.** Distributed monolith, shared database, no observability outrank others.
- **Quick wins.** Add a missing timeout; document a contract; add a dead-letter queue.
- **Backlog.** Split a shared database; modularise before extracting more services; add tracing.
- **Confirmed strengths.**

## Forbidden conclusions

- "It is fine because we have many services."
- "It is fine because we use Kubernetes."
- "It is fine because each service is in its own repo."

The properties are independent deployment, data ownership, explicit contracts, failure handling, observability. Number of services and platform are operational facts, not architectural validations.
