# 05. Relationship with Other Disciplines

## EDA vs Microservices

Composable but distinct.

- Microservices is a deployment pattern.
- EDA is a communication paradigm.

Microservices can use sync, async, or both. EDA can run inside a monolith (in-process events) or across services (broker-mediated events). They often appear together because async events fit microservices' loose coupling needs.

## EDA vs Hexagonal / Clean / Onion

Composable. Inside an EDA system, each component (service, function, module) has its own internal architecture. Hexagonal/clean/onion are natural fits because they isolate the core from the broker:

- The event publisher is a driven adapter implementing an event-bus port.
- The event consumer is a driving adapter that translates the incoming event into a call on a driving port.
- The application core does not know about the broker.

## EDA vs DDD

Composable. Domain events (DDD pattern) are often the building blocks of EDA. The conversion from domain event to integration event is a deliberate step (see DDD skill).

EDA does not require DDD; DDD does not require EDA.

## EDA vs Event Sourcing

Different. Often confused.

- **EDA:** events are how components communicate.
- **Event Sourcing:** events are the source of truth for state.

A system can be EDA without Event Sourcing (events flow between components, but each component persists state directly). A system can be Event Sourcing without EDA (events are persisted as state but not used for inter-component communication). They compose: Event Sourcing inside a component, EDA between components.

## EDA vs CQRS

Composable. CQRS separates read and write models. In EDA, events from the write side feed read models on the read side.

CQRS does not require EDA; EDA does not require CQRS.

## EDA vs Serverless

Natural fit. Serverless platforms have native integrations with event sources. Many serverless systems are event-driven by default.

## EDA vs MVC

MVC is a UI structure. EDA is a communication paradigm at the system level. They operate at different levels; an EDA system can have a UI structured with MVC.

## Summary

| Discipline | Type | Relationship |
|---|---|---|
| Microservices | Deployment architecture | Composable; often paired. |
| Hexagonal / Clean / Onion | System architecture | Composable internally. |
| DDD | Design method | Composable; domain events feed EDA. |
| Event Sourcing | Persistence pattern | Different but composable. |
| CQRS | Pattern | Composable. |
| Serverless | Runtime architecture | Natural fit. |
| MVC | UI structure | Composable at UI level. |
