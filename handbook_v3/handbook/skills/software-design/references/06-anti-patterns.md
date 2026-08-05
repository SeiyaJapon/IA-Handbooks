# 06. Anti-patterns

Cross-discipline design failures.

## Pattern cargo cult

Patterns applied because they are well-known, not because they solve a problem. Strategy, Factory, Observer everywhere. Ceremony grows; clarity shrinks.

Fix: apply a pattern when the problem matches the pattern's intent.

## Anaemic domain in rich domain context

Entities are data holders; logic in services. The team thinks they are doing DDD because there is a `domain/` folder.

Fix: move behaviour into entities and value objects. If the domain is genuinely thin, transaction script may be the right design.

## Premature abstraction

Interfaces for every class, factories for every creation, "in case we need to swap implementations". Most never get swapped.

Fix: introduce abstractions when there is a concrete need. YAGNI.

## Generic hell

Generic types and base classes that try to handle every case. End up not fitting any case well.

Fix: prefer specific types named after the domain. Generics where they pay (collections, query result handling), not for everything.

## God class

A class with hundreds of methods, dozens of dependencies, thousands of lines.

Fix: split by responsibility. Multiple cohesive classes are clearer than one mega-class.

## Refused bequest

A subclass that does not need the parent's methods, or has to override them with no-ops.

Fix: composition over inheritance. The subclass is not really a subtype.

## Data envy

A class that reaches into another class's data extensively. The behaviour belongs in the other class.

Fix: tell, don't ask. Move the behaviour to the class with the data.

## Long parameter list

Methods with eight, ten, fifteen parameters. Hard to remember, easy to mismatch.

Fix: parameter object, value objects, or split the method.

## Primitive obsession

Money as `number`, currency as `string`, IDs as `string`. Domain meaning lost.

Fix: value objects.

## Magic strings and numbers

`if (status === "active")`, `setTimeout(..., 86400000)`. Meaning hidden.

Fix: enums, constants, named values.

## Comments instead of clear code

A comment explaining what code does. The code should explain itself.

Fix: rename, refactor, extract. Comments justify why, not what.

## Test-driven over-engineering

Code shaped entirely by what tests need, with tests that mock everything. Tests pass; the code is awkward.

Fix: tests verify behaviour. Mock at boundaries (ports), not at every class.

## Output

When reviewing design, look for these patterns. Pattern cargo cult, anaemic domain, premature abstraction, and god classes are the most common.
