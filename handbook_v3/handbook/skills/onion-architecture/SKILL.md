# Onion Architecture

## Purpose

Evaluate whether a design follows Onion Architecture principles: domain at the core, dependencies pointing inward, infrastructure at the outer ring.

## Responsibilities

- Identify the rings present: Domain Model → Domain Services → Application Services → Infrastructure / UI
- Detect dependencies violating the inward-only rule
- Evaluate whether the domain model is free from infrastructure concerns
- Assess whether application services orchestrate without containing business rules
- Review port (interface) definitions and their placement
- Detect infrastructure implementations leaking into inner rings
- Evaluate whether the outer ring depends on abstractions, not implementations

## Instructions

- Identify which ring each changed file or class belongs to
- Check domain model: no framework imports, no database types, no HTTP concerns
- Check domain services: operate only on domain model types and interfaces
- Check application services: orchestrate use cases, call domain services, use infrastructure ports
- Check infrastructure: implements ports defined in inner rings; depends inward
- Check that interfaces (ports) are defined in the ring that needs them, not in the ring that implements them
- Ask: if I replace the database adapter, does anything in the domain or application ring change?

## Heuristics

Treat as stronger concerns when:

- Domain model importing ORM base classes, HTTP types, or external SDK types
- Application service containing business rules that belong in the domain
- Infrastructure type (e.g. database row, HTTP response) used directly in the domain layer
- Interface defined in the infrastructure ring and imported by the application or domain ring
- Direct instantiation of infrastructure classes inside application or domain services

Treat as acceptable when:

- Simple modules where strict ring separation would add more complexity than value
- Shared value types (e.g. enums, primitive wrappers) that cross rings without behavior

## Rules

- Dependencies point inward only — outer rings depend on inner rings, never the reverse
- Business rules live in the domain model and domain services — not in application services
- Infrastructure implements abstractions defined by inner rings
- Replacing infrastructure must not require changes in inner rings

## Activity Traceability

🔧 Loading skill: `onion-architecture`
