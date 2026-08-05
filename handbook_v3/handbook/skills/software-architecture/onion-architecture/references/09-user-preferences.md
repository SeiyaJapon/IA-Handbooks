# 09. User Preferences (Francisco)

Project conventions for Onion Architecture in Francisco Pérez's projects at Kintai.

## When onion is acceptable

The default backend architecture in this project is **hexagonal**. Onion is acceptable for a specific repo when:

- The forces match the inward-dependency family.
- The domain has meaningful cross-entity behaviour worth an explicit Domain Services ring.
- The team's vocabulary genuinely speaks onion.
- A different sibling (hexagonal, clean) is not already in use in the same monorepo.

Mixing siblings across the monorepo is rejected. One vocabulary per project.

## Vocabulary

When onion is in use, the codebase uses onion's vocabulary consistently:

- **Rings** (Domain Model, Domain Services, Application Services, External Layer).
- **Domain Service**, **Application Service** as different concepts in different rings.
- **Interfaces declared by inner rings**, implemented by the External Layer.

The terms "port", "driving / driven adapter" (hexagonal), "Interactor", "Boundary", "Controller / Presenter / Gateway", "circle" (clean) do not appear in code, comments, or documentation.

## Layout

A typical onion codebase in this project:

```
src/
├── domain/                         (Domain Model ring: entities, value objects)
├── domain-services/                (Domain Services ring)
├── application/                    (Application Services ring: use cases + interfaces)
└── external/                       (External Layer: persistence, transport, framework)
```

Variants:

- `infrastructure/` instead of `external/` is acceptable when consistent.
- The team may organise the External Layer by capability sub-folder (`external/persistence/`, `external/web/`, etc.) for clarity. The architecture is the dependency direction and the ring partition.

## Domain Services discipline

Domain Services are populated only with **cross-entity domain logic**. The ring is not a dumping ground.

Empty Domain Services ring triggers an evaluation: should we be using hexagonal or clean instead?

Generic services or technical utilities do not belong in this ring; they go in External Layer or a utility module.

## Interfaces

Interfaces that the inner rings need from the outside are **declared in the inner ring that needs them** (typically Application Services), not in the External Layer.

Naming follows the project's TS conventions: `<Capability>RepositoryInterface`, `<Capability>PortInterface` are accepted; the term "port" in identifier names is a TS convention here, not a hint of hexagonal vocabulary in onion's sense.

## Framework coupling

- Domain Model, Domain Services, Application Services are framework-agnostic.
- DI is manual through constructors. Framework decorators are forbidden in inner rings.
- Wiring lives in the External Layer (composition root).

## Communication style

Avoid em dash in normal prose. When adding an aside or clarification, prefer parentheses or a separate sentence. Do not use em dash as a default punctuation device. Only keep em dash when quoting existing text or when explicitly discussing the character itself.

## Open conventions (to confirm)

- Whether the canonical folder names are `domain/`, `domain-services/`, `application/`, `external/`, or framework-default approximations.
- Whether onion-style projects in this monorepo are accepted at all (default: hexagonal is preferred unless the forces specifically favour onion's explicit Domain Services ring).
- Whether interfaces declared by inner rings follow the suffix `Interface` (consistent with the project's TS conventions) or onion's literature default.
- Whether External Layer is sub-organised by capability (`persistence/`, `web/`, etc.) by convention or left flat.

These are open. Do not assume; ask before treating any of them as a rule.
