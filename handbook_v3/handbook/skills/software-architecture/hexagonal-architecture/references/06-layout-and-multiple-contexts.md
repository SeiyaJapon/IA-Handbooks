# 06. Layout and Multiple Contexts

The folder layout of a hexagonal codebase is a **consequence** of the architecture, not its definition. Two projects can both be hexagonal with different layouts. What matters is that the dependency rule is enforced and that the three layers are recognisable.

This document lists the layouts that are valid, the rules they must honour, and how hexagonal applies when several units (bounded contexts, modules, services) coexist in one repository.

## Single-application layout

The most basic hexagonal layout, a single application with no internal subdivisions:

```
src/
├── domain/
├── application/
├── infrastructure/
└── main.ts
```

Each layer holds its own files. `main.ts` (or its equivalent) is the composition root: it lives outside the three layers conceptually and is allowed to import from `infrastructure/` and `application/` to wire everything.

## Multiple-context layout (bounded-context-first)

When the application has several bounded contexts (DDD vocabulary), each context owns its own three layers:

```
src/
├── auth-context/
│   ├── domain/
│   ├── application/
│   └── infrastructure/
├── product-context/
│   ├── domain/
│   ├── application/
│   └── infrastructure/
├── receivables-simulator-context/
│   ├── domain/
│   ├── application/
│   └── infrastructure/
├── shared-context/                      (shared kernel; domain/application/infrastructure as needed)
├── infrastructure/                      (server-wide infrastructure not owned by any single context)
├── app.module.ts                        (composition root)
└── main.ts
```

Rules:

- Each context's `domain/`, `application/`, and `infrastructure/` are independent.
- A context never imports from another context's `domain/` or `application/`. Cross-context communication happens through events or a published interface.
- The shared kernel (if any) is the only model that crosses context boundaries, and it is treated as a third dependency (see DDD skill for how shared kernels work).
- Server-wide infrastructure (the database client shared across contexts, framework wiring) lives in a top-level `infrastructure/` folder, not as a fake context.

## Multiple-context layout (layer-first)

A less common shape: top-level folders are the three layers, and bounded contexts live inside each.

```
src/
├── domain/
│   ├── auth/
│   ├── product/
│   └── receivables-simulator/
├── application/
│   ├── auth/
│   ├── product/
│   └── receivables-simulator/
├── infrastructure/
│   ├── auth/
│   ├── product/
│   └── receivables-simulator/
└── main.ts
```

Rules:

- Same dependency rule applies (`infrastructure/` → `application/` → `domain/`).
- Cross-context imports inside a layer are forbidden (`domain/auth/` cannot import from `domain/product/`).
- The composition root wires the adapters across all contexts.

This layout is a valid choice but in practice less common because moving a context (extracting it to a new repo, splitting it across services) requires touching three top-level folders instead of one.

## Mixing the two is forbidden

Pick one organising axis and apply it consistently. The two valid shapes above never mix.

The failure mode:

```
src/
├── domain/                 (layer)
├── application/            (layer)
├── infrastructure/         (layer)
├── auth/                   (context with its own three layers inside)
├── product/                (context with its own three layers inside)
├── prisma/                 (framework module sitting next to the layers)
└── ...
```

This is two organising models in the same tree. It is a smell. Treat it as a finding to fix.

Concretely:

- `src/<feature>/` next to `src/domain/`, `src/application/`, `src/infrastructure/` is the failure mode.
- Framework module files (`PrismaModule`, `AuthModule`) belong inside `infrastructure/` (layer-first) or inside their bounded context's `infrastructure/` (context-first), never as a peer of the layer folders.

## Inside `infrastructure/`: subfolders by adapter type

Inside `infrastructure/`, adapters are grouped by type **only when there is more than one of the kind**:

- `infrastructure/http/` when there are multiple HTTP controllers.
- `infrastructure/persistence/` when there are multiple repository implementations or persistence-adjacent files.
- `infrastructure/messaging/` when there are multiple message consumers or producers.
- `infrastructure/clients/` when there are multiple external service clients.

When there is a single file of a kind, it lives at the layer's root without a subfolder. Subfolders that hold one file are ceremony.

## Empty layers

A layer is created when there is content for it.

- A bounded context that is a thin adapter to a generic subdomain may have only `infrastructure/`.
- A context that is pure orchestration may not have a meaningful `domain/`.

Empty `domain/` or `application/` folders with only a README are noise. Remove them. The layer is recognised by its content, not by its folder existence.

## Composition root

The composition root is the place that wires concrete adapters to the ports the application core declares.

- In a NestJS application, the composition root is the root module file (`app.module.ts`) and the per-context module files. They live in `infrastructure/` (or in the context's `infrastructure/`).
- In a non-framework application, the composition root is `main.ts` or an explicit DI configuration file.

The composition root is the only place that sees both ports and concrete adapters. Everything else either knows ports (the core) or knows technologies (the adapters), never both.

## Multiple repositories vs monorepo

Hexagonal does not prescribe whether contexts live in one repository or many. Both are valid.

- **Monorepo with several contexts**: each context is a top-level folder under `src/`. The boundary is enforced by code review, lint rules, and the dependency direction. The team treats each context as if it were a separate library.
- **Multiple repositories, one context per repo**: each repository follows the single-application layout (or a layered shape) with its own three layers.

The decision is operational (deployment, ownership, release cadence), not architectural. Whether a context shares a repo with others does not change its hexagonal structure.

## Anti-patterns in layout

- **`presentation/` layer.** Does not exist in hexagonal. HTTP and other transports are infrastructure.
- **Mixed organisational axes.** Layers in the root next to features in the root.
- **Framework modules outside `infrastructure/`.** `PrismaModule` next to `domain/` is a violation of the boundary.
- **Shared mutable state across contexts.** Two contexts that read or write the same global object are coupled outside the layer rules.
- **One global `infrastructure/` for everything.** When several contexts coexist and each context has its own infrastructure, conflating them into a single top-level `infrastructure/` collapses the boundary.
- **Layer folders that exist but are empty.** They suggest structure that does not exist.
- **A `core/` folder that duplicates `domain/`.** The domain layer is the core. A second name for the same thing is confusion.

## Output

For the layout to be considered correct, the team can answer:

- Which organising axis is in use (bounded-context-first, layer-first, single-application)?
- Where does each context live?
- Inside each context, are the three layers present and consistent?
- Inside `infrastructure/`, how are adapters grouped, and does the grouping match the "more than one file" rule?
- Where is the composition root?
- Are there violations (`presentation/`, framework modules in the root, mixed axes)?

If the layout cannot be classified, fix the layout before continuing the review.
