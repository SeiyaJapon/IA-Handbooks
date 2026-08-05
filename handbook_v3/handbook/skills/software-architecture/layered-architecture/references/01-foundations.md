# 01. Foundations

## Origin

Layered Architecture is the classical structural pattern for enterprise software. It predates the inward-dependency family (hexagonal 2005, onion 2008, clean 2012) by decades and was the dominant architecture in the 1980s and 1990s for business applications, and remains common in framework-driven projects today.

There is no single author. The pattern is documented in books such as Martin Fowler's "Patterns of Enterprise Application Architecture" (2002), the GoF literature, and earlier SDLC texts. The shape (presentation / business / data) emerged from practice across multiple traditions.

## Core idea

Stack the system in layers. Each layer has a specific responsibility. Each layer calls the next one below. The most common shape is **three-tier**:

- **Presentation**: how the system communicates with users or callers.
- **Business** (or Service): the rules of the system.
- **Data** (or Persistence): how state is stored and retrieved.

Larger systems add layers: integration, application services, domain, infrastructure. The number of layers is not what makes layered layered; the **downward dependency direction** is.

## What layered solves

Before layered, applications were often unstructured: a function that took an HTTP request, queried the database, formatted the response, and sent it back, all in one place. Layered separates these responsibilities so that:

- The presentation can change without rewriting business rules.
- The data store can change (sort of) without rewriting business rules.
- The team can split work along layer boundaries.

## What layered does not solve

- **Dependency on the data shape.** In classical layered, the business layer is typed in terms of the data layer (rows, ORM entities). When the database changes, the business layer changes too. This is the main weakness that hexagonal/clean/onion fix by inverting the dependency.
- **Multiple driving mechanisms.** The presentation layer is typically tied to one transport (HTTP, or desktop UI). Adding a second (CLI, queue) often means duplicating presentation-layer logic.
- **Rich domain isolation.** Layered does not protect the domain from framework or persistence concerns; it only separates them at the file level.

## When layered applies

Apply layered when:

- The domain is **moderately complex**: validation, workflows, simple rules, but no rich invariants.
- The lifetime is **mid-term**: a few months to a couple of years.
- The team is **small**, single context.
- The framework imposes a layered shape and the team accepts it.
- The cost of hexagonal/clean/onion ceremony is not justified.

## When layered does not apply

Do not apply layered when:

- The domain is **rich**: invariants, lifecycle, business vocabulary that must be protected.
- The lifetime is **long-term**: years. The data-shaped business layer becomes a liability.
- The system is **multi-team / multi-context**: layered does not provide context boundaries.
- The system needs **multiple driving mechanisms** sharing the same business logic.

## How to decide

Ask in order:

1. Is the domain rich or moderate?
2. Is the lifetime long-term or mid-term?
3. Are there multiple teams or contexts?
4. Are there multiple driving mechanisms (HTTP + CLI + queue) calling the same logic?

If the domain is rich, the lifetime is long, or there are multiple contexts/transports, prefer hexagonal/clean/onion. If not, layered is fine.

## Common misreadings

- **"Layered is legacy."** Not always. For the right forces, layered is the simplest fit. Calling it legacy is fashion talking.
- **"Layered is just folders."** No. The dependency direction is what makes it layered.
- **"Layered is the same as MVC."** No. MVC structures the presentation; layered structures the whole system. They can coexist (MVC inside the presentation layer of a layered system).
- **"Layered means three layers."** Three is the most common shape. Some layered systems have four, five, or more layers. The number is not the defining property.

## What to do when layered does not apply

- For rich-domain, long-lifetime systems: pick **hexagonal**, **clean**, or **onion**.
- For multi-team systems: pick **microservices** (with hexagonal internally) when the forces justify the operational cost.
- For UI-heavy small systems: **MVC** inside a thin layered shell may be enough.

These decisions are covered in `software-architecture/SKILL.md` and `references/02-how-to-choose-an-architecture.md`.
