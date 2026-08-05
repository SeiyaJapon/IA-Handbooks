# 02. Design Methods Overview

Design methods describe how to model the inside of a component. Different methods fit different forces.

## Domain-Driven Design (DDD)

For rich domains with business rules, lifecycle, and ubiquitous language. Aggregates, value objects, domain services, domain events, application services.

When to apply: rich domain, long-lived codebase, vocabulary owned by the business. See `software-design/ddd/`.

## Transaction script

A simple procedural style: each business operation is a function that does what it needs to do, calling helpers. No rich domain model.

When to apply: thin business logic, short workflows, one developer or small team, prototype.

## Active record

Each entity is a class that knows how to load and save itself. The domain model and the persistence model are one.

When to apply: shaped-by-DB code, simple CRUD-with-some-logic, framework-driven projects (Rails, Django).

## Table module

One class per table. The class holds methods that operate on the table's rows.

When to apply: data-centric processing, reporting, analytics-style code.

## Service layer (anaemic domain)

Entities are data holders; logic lives in services. Common in layered codebases.

When to apply: as an interim shape, as a fallback when DDD is over the top. Often a smell when long-lived rich domains end up here.

## Functional core

Pure functions process data; side effects are isolated at the boundary.

When to apply: data transformations, pipelines, code where testability matters most. Composable with any of the above when the language supports it.

## Choosing

Ask:

1. Is the domain rich (invariants, lifecycle, vocabulary)?
2. Is the codebase long-lived?
3. Are there many developers?
4. Are testing requirements strict?

Multiple yeses: DDD or functional core.

Multiple noes: transaction script or active record.

Mixed: hybrid is normal. Different parts of one codebase can use different methods.
