# 03. Choreography vs Orchestration

When a workflow spans multiple components, two coordination styles exist.

## Choreography

Each component reacts to events independently. No central coordinator. The workflow emerges from the chain of events.

Example:

- Order service emits `OrderPlaced`.
- Payment service consumes it, processes payment, emits `PaymentApproved` or `PaymentRejected`.
- Order service consumes the result, emits `OrderConfirmed` or `OrderCancelled`.
- Notification service consumes `OrderConfirmed`, emits notification.

Pros:

- No single point of failure.
- Components evolve independently (add a new consumer without changing producers).
- Naturally scales: each component can scale at its own pace.

Cons:

- The workflow is invisible. No one place describes the full chain.
- Debugging is harder: trace events across components.
- Deadlocks and infinite loops are possible if events cycle.

## Orchestration

A coordinator (saga, process manager, workflow engine) drives the sequence. The coordinator listens to events and emits commands to next steps.

Example:

- Order saga starts on `OrderPlaced`.
- Saga calls payment service.
- On `PaymentApproved`, saga calls inventory service.
- On `InventoryReserved`, saga emits `OrderConfirmed`.
- On any failure, saga compensates.

Pros:

- The workflow is visible in the saga.
- Easier to debug: the saga is the source of truth for the workflow state.
- Compensation is explicit.

Cons:

- The saga becomes a hub with knowledge of multiple components.
- Single point of evolution: changes to the workflow change the saga.
- The saga can become a god orchestrator if it grows.

## Choosing

Per workflow, ask:

- Is the workflow simple and unidirectional? Choreography is fine.
- Is the workflow complex with many branches and compensations? Orchestration is clearer.
- Are the components naturally autonomous? Choreography fits.
- Do the components need shared knowledge of the workflow? Orchestration fits.

Mixed systems are normal: some workflows are choreographed, some orchestrated.

## Sagas

A saga is a long-running workflow with explicit compensation steps. Two flavours:

- **Choreographed saga:** the saga is implicit; each component knows what to do.
- **Orchestrated saga:** an explicit saga component drives the steps.

Both are valid implementations of the saga pattern.

Sagas need:

- **State.** The saga's progress is persisted.
- **Compensation.** Each step has an undo path for failures.
- **Observability.** Stuck sagas must be detectable and inspectable.
- **Idempotency.** Steps can be retried safely.

## Workflow engines

Some platforms provide workflow engines: AWS Step Functions, Temporal, Cadence, Argo Workflows. These are orchestrators with state, retries, compensation, and observability built in.

When the workflow is complex enough, a workflow engine is preferable to hand-rolled orchestration.

## Anti-patterns

- **Choreography without observability.** A workflow is broken; nobody can see why.
- **Orchestration that grows into a god service.** The orchestrator becomes a monolith of workflows.
- **Implicit cycles.** Component A reacts to B's events; B reacts to A's events; infinite loop.
- **Saga without compensation.** Failures leave the system in inconsistent state.
- **Long synchronous chain disguised as orchestration.** The orchestrator calls each step synchronously; the workflow blocks on each call.

## Output

For each multi-step workflow, the team can answer:

- Choreography or orchestration?
- If saga: where is the saga state? What are the compensations? Is it observable?
- Is the workflow tested end-to-end?
