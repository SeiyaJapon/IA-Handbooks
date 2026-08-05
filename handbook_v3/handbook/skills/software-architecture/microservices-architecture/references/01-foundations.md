# 01. Foundations

## Origin

Microservices emerged in the early 2010s as an evolution of Service-Oriented Architecture (SOA), popularised by companies like Netflix and Amazon, and codified in writings by Martin Fowler, James Lewis, Sam Newman ("Building Microservices", 2015) and others. The term "microservice" appeared around 2011-2012; the practice predates the name.

The defining shift from SOA to microservices: each service owns its data and is independently deployable. SOA tolerated shared databases and centralised messaging buses; microservices does not.

## What microservices solves

- **Independent release cadence.** Multiple teams can deploy without coordinating across the whole system.
- **Asymmetric scaling.** Hot parts of the system scale independently of cold parts.
- **Asymmetric change rates.** Parts that change weekly are not slowed by parts that change yearly.
- **Failure isolation.** A failure in one service does not necessarily bring down others.
- **Technology diversity.** Different services can use different languages, frameworks, databases (when justified).

## What microservices does not solve

- **Bad architecture.** Microservices on top of a tangled monolith is a distributed tangle.
- **Domain modelling.** Microservices is a deployment shape; the model still has to be designed (DDD or otherwise).
- **Lack of operational maturity.** Microservices need observability, tracing, deployment automation. Without those, microservices are an outage generator.

## When microservices applies

- **Multiple teams** need to deploy independently.
- **Asymmetric change rates** between parts of the system.
- **Scaling profiles differ** across parts of the system.
- **Operational maturity** is in place.

## When microservices does not apply

- Single team. The coordination cost of microservices exceeds the benefit.
- Single bounded context. There is no natural service boundary.
- Operational immaturity. Microservices fail in production without monitoring and tracing.
- "We want to be modern". Cargo cult. Forces decide.

## Common misreadings

- **"Microservices is small services."** Size is incidental. Independence is the property.
- **"We use Kubernetes, so we are microservices."** Kubernetes is a platform; microservices is an architecture.
- **"Each NestJS app is a microservice."** Maybe. If they share a database or cannot be deployed independently, no.
- **"DDD requires microservices."** No. Bounded contexts can be modules in a monolith.
- **"Microservices is the modern monolith replacement."** Not always. A modular monolith is often the right step before microservices, or the permanent destination.

## Decision

Ask:

1. Multiple teams with independent release cadence?
2. Asymmetric change rates between parts?
3. Independent scaling needs?
4. Operational maturity (observability, deploy automation, tracing) in place?

Two or more no: do not adopt microservices. A modular monolith is likely the right answer.
