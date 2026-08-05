# 05. Anti-patterns

The recurring failures of layered architecture in real codebases. Each is a pattern to recognise, name, and reject during reviews.

## Direction violations

### Skipping a layer

A class in the presentation layer calls the data layer directly, bypassing the business layer.

- Why it fails: business rules that should apply do not. Two callers reaching the same data through different paths get different behaviour.
- How to recognise: a controller imports a repository or a DAO.
- Fix: route the call through the business layer. Even if the business method is one line, that one line is the place where rules can be added without changing the caller.

### Reverse dependency

The data layer imports from the business layer, or the business layer imports from the presentation layer.

- Why it fails: the lower layer takes on responsibilities of the upper one. The architecture is upside down on that path.
- How to recognise: a repository imports a service; a service imports a controller.
- Fix: invert. Move the responsibility upward. The data layer should not validate or decide; it should execute.

### Bidirectional dependency between layers

Presentation and business import each other.

- Why it fails: the layers are not layers any more; they are one tangled piece.
- How to recognise: cycles in the dependency graph between layer modules.
- Fix: break the cycle. One of the imports is wrong; identify and remove.

## Layer-mixing violations

### Fat controller

A controller (presentation) that contains business rules.

- Why it fails: rules in the wrong layer cannot be reused by another caller (CLI, queue consumer). Tests of the rule require booting HTTP infrastructure.
- How to recognise: an `if` in the controller that decides whether the operation is allowed; a calculation in the controller that depends on business attributes.
- Fix: move the rule to the business layer. The controller becomes a translator between HTTP and the business call.

### Fat service / god service

A service (business) that does everything: input validation, domain rules, query construction, response shaping.

- Why it fails: the class is unmaintainable. Its dependencies sprawl. Tests are slow because they need every collaborator.
- How to recognise: a service class with thirty methods, hundreds of lines, dependencies on many repositories and external clients.
- Fix: split by use case. One service class per business operation, or one method per operation in a thin orchestration class.

### Smart repository

A repository (data) that contains business rules: validates before saving, decides what is allowed.

- Why it fails: business rules in the data layer are not reused by other paths. Two callers (one through the business layer, one bypassing) get inconsistent behaviour.
- How to recognise: validation logic, derived value computation, or rule enforcement inside repository methods.
- Fix: move the rule to the business layer. The repository executes; it does not decide.

### Anaemic business layer

A business layer that is a thin pass-through: services that just call repositories and return their result, with no rules.

- Why it fails: the business layer adds no value. The presentation layer could call the data layer directly with the same effect (and indeed often the team starts doing that, then the layer becomes meaningless).
- How to recognise: every service method is one line of "return repository.find/save/...".
- Fix: either there are no business rules (in which case the system is CRUD and layered may still apply, but the business layer is honestly a pass-through and the team can name it that), or there are rules that escaped elsewhere (in which case bring them back).

## Type-leak violations

### Database types in the business layer

The business layer is typed in terms of database rows, ORM entities, or ORM-generated types.

- Why it fails: a change in the database schema requires a change in the business code. The business layer is shaped by storage decisions.
- How to recognise: business methods that return ORM entities; business types that have ORM annotations.
- Fix: define business types separately. Repositories map between storage types and business types. (At this point the architecture is moving towards hexagonal/clean; that is a deliberate migration, not a layered tweak.)

### Transport types in the business layer

The business layer accepts HTTP requests or returns HTTP responses.

- Why it fails: the business layer is tied to one transport. Adding CLI or queue requires duplicating presentation logic.
- How to recognise: business methods with parameters typed as `Request`, returning `Response` or DTOs shaped by HTTP.
- Fix: define business-level command/query/result types. The presentation layer translates between HTTP and these.

### Database types in the presentation layer

The presentation layer is typed in terms of database rows.

- Why it fails: the presentation is shaped by storage. Changing the storage forces a presentation rewrite.
- How to recognise: a controller that returns ORM entities directly as JSON.
- Fix: define presentation DTOs. The business layer (or a thin mapper) translates business types to DTOs.

## Framework-coupling violations

### Framework types in the business layer

The business layer imports framework types (`@Injectable`, `@Component`, framework lifecycle interfaces).

- Why it fails: the business is coupled to the framework. Replacing or upgrading the framework requires touching business code.
- How to recognise: framework decorators in business classes.
- Fix: keep the framework wiring in the presentation layer (or in a separate composition root). Business classes take collaborators through plain constructors.

### Framework as the architecture

The team treats the framework's default structure as "the architecture" without enforcing additional rules.

- Why it fails: the framework's defaults are its opinion, not necessarily what the project's forces require. Different versions of the framework have different opinions.
- How to recognise: the answer to "what is the architecture?" is the framework's name.
- Fix: separate framework from architecture. The framework lives in the presentation and data layers; the architecture is the rules the team enforces on top.

## Variant misuse

### Adding layers as ceremony

The team adds a service layer, an integration layer, and an infrastructure layer "for cleanliness", without any of them having a clear responsibility.

- Why it fails: layers without responsibilities are noise. Contributors do not know where to put new code.
- How to recognise: layers that contain pass-through code or only one or two files with no obvious distinguishing role.
- Fix: collapse. Three layers is fine for most projects.

### Adopting hexagonal vocabulary on layered structure

Folders are named `domain/`, `application/`, `infrastructure/`, but dependencies still flow downward.

- Why it fails: confusion. The vocabulary suggests hexagonal; the rules are layered. Reviewers cannot tell which architecture is in force.
- How to recognise: hexagonal-style folder names, but business code typed in terms of data types.
- Fix: pick one. If layered is intended, use layered names (presentation, business, data). If hexagonal is intended, invert the dependencies (see migration paths in `software-architecture/references/05-migration-paths.md`).

## Sustainability violations

### Layered without enforcement

Documented as layered. Contributors gradually skip layers, leak types, mix responsibilities. Nothing breaks because the rules are not enforced.

- Why it fails: the architecture decays. After a year, the system is layered in name only.
- How to recognise: a layered architecture document, but a dependency graph that shows arrows everywhere.
- Fix: add enforcement. Lint rules, dep-graph checks, code review focused on the rules.

### Layered for too long

The system started layered when the domain was simple, the lifetime was unclear, and the team was small. Years later, the domain is rich, the lifetime is long, the team has grown. The architecture still says layered.

- Why it fails: cost compounds. The data-shaped business layer leaks types upward; every change ripples; refactor cost grows.
- How to recognise: persistent friction in business changes; data layer migrations that rewrite half the system.
- Fix: migrate. See `software-architecture/references/05-migration-paths.md` for layered → hexagonal/clean/onion.

## Output

When reviewing a layered codebase, look for these patterns by name. Each is a finding. Cluster them by category (direction, mixing, type-leak, framework, variant, sustainability). Direction and type-leak findings take priority because they invalidate the layered separation; the others are localised.
