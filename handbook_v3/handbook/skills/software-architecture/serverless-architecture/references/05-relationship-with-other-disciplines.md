# 05. Relationship with Other Disciplines

## Serverless vs Long-Running Services

Two runtime models. Both deploy services. Both can host any internal architecture.

- Long-running: persistent process (containers, EC2, ECS, processes). Owns the runtime, holds state in memory, no cold starts after start, runs forever (until restart).
- Serverless: short-lived functions. Vendor manages runtime, cold starts, time limits, statelessness across invocations.

Choice depends on workload (see `01-foundations.md`).

## Serverless vs Hexagonal / Clean / Onion

Composable. The function handler is a driving adapter; the application core lives inside the function (or in a shared library imported by the function).

A serverless function with hexagonal internals is the recommended shape: handler is platform-coupled, core is portable.

## Serverless vs Microservices

Composable. A microservice can be deployed as a function (or a set of functions). The microservice boundary is one decision; the runtime is another.

Some teams treat each function as a microservice. That is incorrect: a function is a deployment unit, not a context boundary. Multiple functions can serve one bounded context (one per use case).

## Serverless vs MVC

Composable. The function handler can host MVC inside (Controller, Model, View) for HTTP-shaped functions.

## Serverless vs Event-Driven Architecture

Natural fit. Serverless platforms have native integrations with event sources (SQS, SNS, EventBridge, Kafka, Pub/Sub). Event-driven workflows are the most common serverless use case.

## Serverless vs DDD

Composable. The application core inside a function can be DDD-modelled (aggregates, value objects, domain services). The function is the delivery; the domain lives inside.

## Serverless vs CQRS, Event Sourcing

Composable. Read functions for queries, write functions for commands. Event handlers consume the event store and update read models.

## Summary

| Discipline | Type | Relationship |
|---|---|---|
| Long-running services | Runtime model | Alternative; choice based on workload. |
| Hexagonal / Clean / Onion | System architecture | Composable internally. |
| Microservices | Deployment architecture | Composable; service can deploy as functions. |
| MVC | UI structure | Composable inside HTTP function handlers. |
| EDA | Communication paradigm | Natural fit. |
| DDD | Design method | Composable; domain inside the function core. |
| CQRS | Pattern | Composable. |
| Event Sourcing | Persistence pattern | Composable. |
