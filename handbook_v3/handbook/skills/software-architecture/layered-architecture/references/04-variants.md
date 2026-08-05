# 04. Variants

Real layered codebases rarely have exactly three layers named exactly "presentation", "business", "data". Variants emerged to handle specific concerns. This document records the most common variants and what they add.

The shape of a variant is still layered: dependencies point downward. The layers are renamed, split, or extended.

## Two-tier (client-server)

The simplest layered shape:

```
Client (presentation + business)
    |
    v
Server (data, sometimes with business)
```

Common in early enterprise systems. Largely obsolete in new development.

## Three-tier (canonical)

```
Presentation  ->  Business  ->  Data
```

The canonical shape. See `02-the-three-layers.md`.

## N-tier (four or more layers)

The three-layer shape extended with one or more layers between or beside the canonical three.

Common variants:

### Service layer

A layer between presentation and business, dedicated to orchestrating business calls.

```
Presentation  ->  Service  ->  Business  ->  Data
```

When to use: when the business layer is a collection of fine-grained classes and the presentation needs a coarser facade. The service layer composes business calls into use-case-shaped operations.

### Application layer

In DDD-flavoured layered, the business layer splits into:

- **Domain layer**: entities, value objects, domain services. Pure model.
- **Application layer**: orchestrates domain calls.

```
Presentation  ->  Application  ->  Domain  ->  Data
```

This is closer to the inward-dependency family in shape, but classical layered in dependency direction (the domain still depends on data types in classical layered; in hexagonal/clean/onion, it would not).

### Integration layer

External system calls (third-party APIs, legacy systems) live in their own layer instead of inside data.

```
Presentation  ->  Business  ->  Data           (internal storage)
                       \-->  Integration       (external systems)
```

When to use: when external integrations are numerous or complex, separating them from internal storage clarifies the codebase.

### Infrastructure layer

Cross-cutting concerns (logging, configuration, security primitives, metrics) live in their own layer that can be called by all others.

```
Presentation  ->  Business  ->  Data
       \           |             /
        \-----> Infrastructure <-/
```

The infrastructure layer is special: dependencies into it are allowed from any layer, but it does not depend on any business or data layer. It is closer to a utility module than a layer in the dependency-direction sense.

## Hexagonal-flavoured layered

Some teams adopt the **folder structure** of hexagonal (`domain/`, `application/`, `infrastructure/`) without inverting the dependency direction. The result is layered with hexagonal vocabulary.

This is a smell. Either the dependency direction is inverted (and it is hexagonal), or the structure is layered with hexagonal-style folder names. The latter creates confusion and reviewers cannot tell which architecture is in force.

If the team intends layered, use layered vocabulary (presentation, business, data). If the team intends hexagonal, invert the dependencies.

## Modular monolith on top of layered

A monolith with layered architecture inside, but split into independent modules per business capability or context.

```
Module A: Presentation -> Business -> Data
Module B: Presentation -> Business -> Data
Module C: Presentation -> Business -> Data
```

Each module is layered internally. Modules communicate through explicit module-level contracts (events, public interfaces).

When to use: as a stepping stone before microservices, or as a permanent shape for systems that benefit from module boundaries but do not need network-level isolation.

## Layered with framework-imposed shape

Many web frameworks (Rails, Django, NestJS, Spring) impose a layered or near-layered shape:

- Controllers (presentation).
- Services (business).
- Models or repositories (data).

When the team adopts the framework's default, the architecture is layered by default. This is acceptable when:

- The forces of the project match layered's fit (moderate domain, mid-term lifetime).
- The team enforces the dependency rules even when the framework would let them violate.

The framework's shape is a starting point, not the architecture itself. The architecture is whatever rules the team enforces on top of the framework's default.

## What is NOT a layered variant

These are sometimes confused with layered variants but are different architectures:

- **Hexagonal / clean / onion.** Different dependency direction (inward, not downward).
- **Microservices.** Different structural axis (services as deployment units, not layers).
- **Event-driven architecture.** Different communication paradigm.
- **MVC.** UI structure, not a system-wide layered variant.

## Choosing a variant

Pick the simplest variant that fits the system:

- **Three-tier** is the default. Start there.
- **Add a service layer** only when the presentation needs a coarser facade than the business layer naturally provides.
- **Add an integration layer** only when external systems are numerous enough to clutter the data layer.
- **Add an infrastructure layer** only when cross-cutting concerns are explicit and shared.
- **Modular monolith on layered** when the system has multiple bounded contexts but does not yet justify microservices.

Adding layers is cheap; removing them later is expensive. Default to fewer.

## Output

For a layered variant, the team can answer:

- Which variant is in use, and why?
- What does each layer own, including the added ones?
- Where are the dependencies directed?
- Are framework defaults adopted, modified, or overridden?

Without these, the variant is implicit and contributors place classes wherever the framework or convenience suggests.
