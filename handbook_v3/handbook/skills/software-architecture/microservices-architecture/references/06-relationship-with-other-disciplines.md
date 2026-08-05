# 06. Relationship with Other Disciplines

## Microservices vs Monolith

A monolith is a single deployable unit. Microservices is many independently deployable units. Both can be well-architected internally; both can be poorly architected.

A **modular monolith** sits between: bounded contexts as modules with explicit contracts, but one deployment unit. Often the right step before microservices, or the permanent destination.

## Microservices vs Hexagonal / Clean / Onion

Composable. Each microservice is one of these architectures internally (recommended). The microservice boundary is one decision; the internal architecture is another.

A microservice without a clean internal architecture is a small ball of mud.

## Microservices vs Layered

A microservice can be layered internally for moderate-complexity services. For long-lived rich-domain services, hexagonal/clean/onion is preferred internally.

## Microservices vs Serverless

Composable. A service can be deployed as a Lambda function, a container, or a long-running process. The deployment is operational; the service boundary is architectural.

## Microservices vs MVC

Composable. The HTTP entry point of each service can use MVC inside.

## Microservices vs Event-Driven Architecture

Composable. Microservices often use events as the primary communication mode (microservices + EDA). They are not the same: microservices is the deployment pattern, EDA is the communication paradigm.

## Microservices vs DDD

Composable. The default mapping is one bounded context per microservice. Bounded contexts are designed for autonomy; that is exactly what microservices needs.

DDD does not require microservices. Microservices does not require DDD (but works much better with it).

## Microservices vs CQRS, Event Sourcing

Composable. Each service can use CQRS internally; some services may use Event Sourcing.

## Microservices vs SOA

SOA is the predecessor. SOA tolerated shared databases and centralised messaging buses; microservices does not. Many SOA systems can be evolved into microservices by giving each service its own data and removing the central bus.

## Summary

| Discipline | Type | Relationship |
|---|---|---|
| Monolith | Deployment | Alternative; modular monolith often the right intermediate. |
| Hexagonal / Clean / Onion | System architecture | Composable; one internal architecture per service. |
| Layered | System architecture | Composable for moderate services. |
| MVC | UI structure | Composable inside the HTTP adapter of a service. |
| Serverless | Runtime architecture | Composable; service deployed as functions. |
| EDA | Communication paradigm | Composable; microservices often use events. |
| DDD | Design method | Composable; one bounded context per service is the default. |
| CQRS | Pattern | Composable. |
| Event Sourcing | Persistence pattern | Composable. |
| SOA | Predecessor | Microservices is SOA without shared databases. |
