# 03. Dependency Direction

The defining property of layered architecture is the **downward dependency direction**: each layer depends on the layer below it, never the other way around.

This document covers the rule, why it is the rule, what violates it, and how it differs from the inward direction of hexagonal/clean/onion.

## The rule

```
Presentation
    |  (depends on)
    v
Business
    |  (depends on)
    v
Data
```

- Presentation depends on Business.
- Business depends on Data.
- Presentation does NOT depend on Data directly.
- Data does NOT depend on Business or Presentation.
- Business does NOT depend on Presentation.

Each layer **knows about** (imports types from, calls methods of) the layer immediately below. It does not know about layers further down or above.

## Why downward

Layered emerged from procedural programming where the "main" function called helpers, helpers called other helpers, until the call reached the system boundary (file, database, network). The structure mirrors the call stack: top-level handlers at the top, low-level operations at the bottom.

The benefit is that the lower layers do not know about the higher ones, so they can be replaced or reused without rewriting upward. The cost is that the lower layers' types leak upward: changes in the data layer's shape ripple into business and presentation.

## What violates the direction

### Skipping a layer

Presentation calls Data directly:

```
Presentation
    |  (skip!)
    +-----> Data
    v
Business
```

Symptoms: a controller that imports a repository directly, executes a query, formats the result.

Why it fails: the business layer is bypassed. Rules that should apply (validation, derived values, transactions) are not applied. Other callers reaching the same data through the business layer get one behaviour; this caller gets another.

### Reverse direction

Data calls Business, or Business calls Presentation:

```
Presentation  <-----+
    |  (reverse!)   |
    v               |
Business  <---------+ (reverse!)
    |               |
    v               |
Data  -------------+
```

Symptoms: a repository that imports a service to validate before saving; a service that imports a controller to format output.

Why it fails: the lower layer takes on responsibilities of the upper one. Reuse breaks. Testing becomes circular.

### Layer mixing

A class lives in one layer but does the job of another:

- A controller that contains business rules.
- A service that issues SQL queries directly.
- A repository that decides what is allowed.

Symptoms: classes named after one layer but doing the work of another.

Why it fails: the layer boundaries are nominal only. Changes to the actual architecture (splitting, replacing, scaling) become impossible because the responsibilities are not where the names say.

## Comparison with inward direction (hexagonal/clean/onion)

The inward-dependency family inverts the relationship:

```
                  Domain (innermost)
                     ^
Application ---------+
   ^
Infrastructure ------+
```

- Infrastructure depends on Application and Domain.
- Application depends on Domain.
- Domain depends on nothing.

The key difference: **lower-level concerns (storage, transport) depend on higher-level concerns (domain, application), not the reverse**. This is achieved through ports (interfaces) defined in the inner layers and implemented in the outer ones.

In layered:

- The data layer is at the bottom, and its types leak upward into the business layer.
- The business layer is shaped by what the data layer offers.

In inward-dependency:

- The domain is at the centre and defines what it needs as ports.
- The infrastructure layer (storage, transport) implements those ports.
- The domain is not shaped by the storage choice.

This is the reason the inward-dependency family is preferred for long-lived rich-domain systems: the domain stays clean even as storage and transports change.

## How to detect violations

### Static checks

- **Dependency graph tools.** A graph of imports between modules shows arrows that go upward or skip layers.
- **Lint rules.** Project-specific rules can forbid imports from `presentation/` to `data/`, or from `data/` to `business/`.
- **Architecture tests.** Unit tests that verify the import structure (e.g. ArchUnit in Java, custom in TS).

### Manual checks

- **Read the imports of every class.** A presentation class importing data types is a smell.
- **Look for SQL or ORM operations outside the data layer.**
- **Look for HTTP types (request, response) outside the presentation layer.**

## Pragmatic exceptions

Two exceptions are sometimes accepted in layered codebases:

### Read-only "thin" passes

In simple read paths, some teams allow the controller to call the repository directly, bypassing the business layer, when the operation is purely a read with no business decision.

- Pro: reduces ceremony for trivial reads.
- Con: the convention is fragile. Future "thin reads" tend to grow business rules over time, then those rules are in the wrong layer.
- Recommendation: do not allow. The cost of the convention exceeds the benefit. If a read genuinely has no business rule, the business layer's method is one line; that one line is not a problem.

### Cross-cutting concerns

Logging, metrics, configuration, security primitives are typically used by all layers. Treating them as a separate "infrastructure" or "cross-cutting" layer that any other layer can call is acceptable, with the rule that cross-cutting code does not depend back on the business layers.

## Output

For the dependency direction to be sound, the team can answer:

- For every class, which layer does it belong to?
- For every import, is the direction valid?
- Are violations enforced (lint, dep-graph, review)?
- What pragmatic exceptions, if any, are allowed, and why?

Without these answers, the direction is documented but not enforced, which is the most common failure mode of layered codebases.
