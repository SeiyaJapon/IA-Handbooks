# 03. Principles Overview

Principles guide design decisions at the class and function level. They are not rigid rules; they are heuristics that produce code that is easier to change, test, and read.

## SOLID

Five principles attributed to Robert C. Martin and others:

- **Single Responsibility (SRP):** a class has one reason to change.
- **Open/Closed (OCP):** open for extension, closed for modification.
- **Liskov Substitution (LSP):** subtypes are substitutable for their base type.
- **Interface Segregation (ISP):** clients should not depend on interfaces they do not use.
- **Dependency Inversion (DIP):** depend on abstractions, not concretes.

See `software-design/solid-principles/` for full coverage.

## Code readability

Names, function size, single responsibility, intent over implementation. The code should read like prose where possible.

See `code-readability/` (root-level skill).

## DRY (Don't Repeat Yourself)

Knowledge should have one representation. Repeated code that represents the same knowledge is consolidated.

Caveat: not every repetition is duplication. Two pieces of code that look similar but represent different things should stay separate. Premature DRY produces wrong abstractions.

## KISS (Keep It Simple)

The simplest design that solves the problem. Avoid speculative generality, premature optimisation, gratuitous abstraction.

## YAGNI (You Aren't Gonna Need It)

Do not add functionality, abstractions, or features for hypothetical future needs. Add them when needed.

## Composition over inheritance

Inheritance is one tool; composition is another. Composition is often more flexible. Deep inheritance hierarchies are a smell.

## Tell, don't ask

Tell objects what to do; do not ask them for their state and decide. Encapsulation.

## Law of Demeter

Each unit should talk only to its immediate friends, not to friends of friends. Reduces coupling.

## Choosing

Principles compose; they do not conflict often. When they conflict, the local force decides:

- A small project may favour KISS and YAGNI over rigid SOLID.
- A long-lived rich domain favours DIP and SRP heavily.
- A data pipeline may favour functional core and DRY.

Apply principles deliberately, not dogmatically. A design that violates SOLID for a documented reason is fine; one that violates SOLID by accident is not.
