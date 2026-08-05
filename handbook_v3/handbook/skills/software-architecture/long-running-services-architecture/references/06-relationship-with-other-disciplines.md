# 06. Relationship with Other Disciplines

## Long-running vs Serverless

The two main runtime models. Choice depends on workload (continuous vs bursty, latency tolerance, statefulness).

A team can mix: some services long-running (HTTP APIs, persistent workers), some serverless (event handlers, scheduled jobs).

## Long-running vs Hexagonal / Clean / Onion

Composable internally. The recommended internal architecture for a long-running service is hexagonal, clean, or onion. Long-running is the runtime; the internal architecture is the structural decision.

## Long-running vs Microservices

Composable. A microservice can be long-running. A monolith can be long-running. Long-running is independent of the deployment topology.

## Long-running vs Layered

A long-running service can be layered internally for moderate-complexity workloads. For long-lived rich-domain services, hexagonal/clean/onion is preferred.

## Long-running vs MVC

MVC is a UI structure. In a long-running web service, MVC may live inside the HTTP entry point.

## Long-running vs EDA

Composable. A long-running queue consumer is the typical pattern for EDA consumers.

## Summary

| Discipline | Type | Relationship |
|---|---|---|
| Serverless | Runtime model | Alternative; choice based on workload. |
| Hexagonal / Clean / Onion | System architecture | Composable internally. Recommended. |
| Microservices | Deployment | Composable. |
| Layered | System architecture | Composable internally. |
| MVC | UI structure | Composable inside the HTTP entry. |
| EDA | Communication paradigm | Composable; long-running consumers are the default. |
