# 07. Anti-patterns

The recurring failures of Onion Architecture in real codebases. Each is a pattern to recognise, name in onion's vocabulary, and reject during reviews.

## Direction violations

### Inner ring imports outer

Domain Model imports from Domain Services or External. Domain Services imports from Application Services or External. Application Services imports a concrete External implementation.

Why it fails: the dependency rule is broken. The inner ring is no longer testable or replaceable in isolation.

Fix: invert through an interface declared by the inner ring.

### ORM annotations on Domain Model

`@Entity`, `@Column` from an ORM on Domain Model classes.

Why it fails: Domain Model depends on the ORM driver (External Layer technology).

Fix: separate persistence shape from the entity. Persistence shape lives in the External Layer.

### Framework decorators on Application Services

`@Injectable`, `@CommandHandler` on Application Services.

Why it fails: Application Services depends on the framework.

Fix: wire in the External Layer; no decorators in inner rings.

## Ring confusion

### Domain Services as a dumping ground

Anything that does not fit in an entity dropped in Domain Services. The ring becomes a generic services folder.

Why it fails: Domain Services lose their meaning. Some entries are domain logic, some are orchestration, some are utilities.

Fix: classify. Domain logic crossing entities → Domain Service. Orchestration of a use case → Application Service. Utility → utility module.

### Application Services collapsed into Domain Services

The team has one ring of "services" that mixes domain logic and orchestration.

Why it fails: when use cases need to evolve independently of domain rules, both change together. The onion-specific axis (intrinsic vs orchestration) is lost.

Fix: split. The line is in `04-domain-services-vs-application-services.md`.

### Empty Domain Services ring

The team adopted onion but the Domain Services ring is empty (every behaviour is on entities or in Application Services).

Why it fails: onion's specific contribution is absent. The team is paying onion's ceremony cost without onion's benefit.

Fix: consider hexagonal or clean instead. They collapse the two service rings; if the domain genuinely has no cross-entity behaviour, those architectures fit better.

### Domain Service that calls infrastructure

A Domain Service queries the database, calls an external API, or imports an ORM type.

Why it fails: Domain Services should be pure domain logic. Reaching infrastructure couples them to External Layer.

Fix: the Application Service loads data through interfaces and passes it to the Domain Service.

## Behaviour leakage

### Domain logic in Application Services

A use case contains business rules instead of orchestrating them.

Fix: move the rule to a Domain Service or to an entity.

### Anaemic Domain Model

Entities with only getters and setters; behaviour all in Domain Services or Application Services.

Fix: move behaviour back to entities. Domain Services are for what crosses entities; Application Services are for orchestration. Entity behaviour belongs to entities.

### External Layer with business rules

A repository, a controller, or a framework module containing business decisions.

Fix: move to inner rings.

## Vocabulary violations

### "Ports" instead of "interfaces"

Hexagonal vocabulary in an onion codebase.

Fix: rename. Onion uses "interfaces declared by inner rings".

### "Driving / driven adapters" instead of "External Layer entries / implementations"

Hexagonal vocabulary.

Fix: rename. Onion does not name adapter roles.

### "Interactor", "Boundary", "Controller / Presenter / Gateway"

Clean vocabulary.

Fix: rename. Onion does not prescribe these.

### "Circles" instead of "rings"

Clean vocabulary.

Fix: rename. Onion uses rings.

## Architectural confusion

### "Onion = hexagonal"

The team uses both vocabularies interchangeably.

Fix: pick one. Migrate the rest.

### "Onion = clean"

Same problem. Either pick onion (and use rings + Domain Services explicitly) or pick clean (and use circles + Boundaries + Interactor + Controller/Presenter/Gateway).

### "Onion = DDD"

DDD is a design method. Onion is a structure. They compose well but are different decisions.

### Folder-as-architecture

The team renames folders to `domain/`, `domain-services/`, `application/`, `external/` without enforcing the dependency rule.

Fix: enforce the rule with tooling and review.

## Output

When reviewing an onion codebase, look for these patterns by name in onion's vocabulary. Direction violations, ring confusion, and the empty Domain Services ring are the most common.

If the Domain Services ring is consistently empty, recommend evaluating hexagonal or clean. Onion's specific contribution does not pay off without it.
