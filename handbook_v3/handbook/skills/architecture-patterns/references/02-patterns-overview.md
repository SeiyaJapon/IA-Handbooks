# 02. Patterns Overview

A catalogue of architectural patterns covered by this handbook (or planned).

## CQRS (Command Query Responsibility Segregation)

Separates write operations (commands) from read operations (queries) into different models. Skill: `cqrs-decision/`.

## Event Sourcing

Stores the sequence of events that produced state, instead of (or in addition to) state itself. Skill: `event-sourcing/`.

## Transactional outbox

A producer writes events to an outbox table inside the same transaction as the state change. A relay reads the outbox and publishes to a broker. Guarantees no events lost between commit and publish.

## Saga

A long-running workflow expressed as a state machine that listens to events and emits commands. Two flavours: choreographed (no central coordinator) and orchestrated (a process manager).

## Anti-corruption layer (ACL)

Translates between contexts when consuming a foreign model. Isolates the consumer's vocabulary from the producer's.

## Materialised view

A pre-computed read shape, populated from source data. Common in CQRS-like setups and reporting systems.

## Repository

An abstraction over persistence at the application level. Used in DDD, hexagonal/clean/onion, and elsewhere.

## Strangler fig

A migration pattern: a new component intercepts old's traffic gradually, replacing it piece by piece.

## Backend-for-frontend (BFF)

A backend tailored to one frontend's needs, sitting between the frontend and a set of microservices.

## API gateway

A single entry point for external clients. Handles authentication, routing, rate limiting, sometimes aggregation.

## Bulkhead

Isolation of resources between components so a failure in one does not exhaust resources used by others.

## Circuit breaker

A component that stops calling a failing dependency for a period, failing fast.

## Patterns NOT in this catalogue

These are sometimes called architectural patterns but are covered elsewhere or out of scope:

- **MVC, MVP, MVVM:** UI patterns covered by the `mvc/` sub-skill of `software-architecture/`.
- **Microservices, serverless:** architectures, not patterns. Covered in `software-architecture/`.
- **Event-Driven Architecture:** paradigm/architecture, not a pattern. Covered in `software-architecture/event-driven-architecture/`.
- **DDD:** design method. Covered in `software-design/ddd/`.
- **Strategy, Factory, Observer:** design patterns. Covered in `software-design/design-patterns/`.

When a question is about one of these, route to the appropriate skill.
