# 01. What is Software Architecture

Software architecture is the **structural decision** about how a system is organised: which parts exist, how they relate, where dependencies go, what is allowed to change without breaking what.

This document defines architecture, separates it from neighbouring disciplines, and lists what is and what is not an architectural decision.

## Definition

A software architecture is the set of decisions that:

- Define the **major parts** of the system (modules, layers, services, components).
- Define how those parts **depend on each other** (which can call which, in which direction).
- Define what is **allowed to change** without forcing changes elsewhere.
- Define what is **inside** the system (the domain) and what is **outside** (transports, persistence, frameworks, third parties).

The architecture is the property of the system, not of the code. Two projects with identical folder structures can have completely different architectures depending on the dependency rules they enforce. A project that calls itself hexagonal but lets the domain import the database is not hexagonal, regardless of the folder names.

## What architecture is not

Architecture is constantly confused with neighbouring concepts. They are different decisions on different axes.

### Not a folder layout

Folders may **reflect** the architecture, but they do not **define** it. A folder named `domain/` does not make the project hexagonal. A folder named `controllers/` does not make it MVC. The dependency rules and the conceptual boundaries are what define the architecture; the folders are a consequence.

### Not a framework

A framework (NestJS, Spring, Laravel, Django) imposes conventions about how to wire things together. Architecture is independent of the framework. The same architecture can be expressed with several frameworks, and the same framework can host several architectures.

A statement like "the architecture is NestJS" is a category error. NestJS is a framework. The architecture is whatever structure was decided independently of NestJS.

### Not a design method

Design methods (DDD, transaction script, active record, table module) decide **how the inside of the core is modelled**. Architecture decides **the structure that surrounds the core**. They compose:

- A hexagonal architecture can host a DDD core.
- A hexagonal architecture can host a transaction-script core.
- A layered architecture can host a DDD core (with weaker boundaries).

Confusing them produces statements like "we are doing DDD, so we have an architecture". DDD is the modelling decision; architecture is a separate decision.

### Not a pattern

Patterns (CQRS, Event Sourcing, Repository, Outbox, Saga, GoF patterns) are **techniques** that apply on top of an architecture. They do not, by themselves, define a system's structure.

- CQRS is a pattern; it can apply inside hexagonal, clean, layered, or no architecture at all.
- Event Sourcing is a persistence pattern; same point.
- Repository is a pattern; it appears in many architectures with different meanings.

A statement like "the architecture is CQRS" is the same category error as "the architecture is NestJS". CQRS is a pattern; the architecture is the structure that hosts it.

### Not a paradigm

Paradigms (functional, object-oriented, procedural) constrain code style and how the language expresses things. They are orthogonal to architecture. A hexagonal codebase can be functional, OO, or procedural; the choice does not change the structural boundaries.

### Not a deployment strategy by itself

Deployment strategies (monolith, microservices, serverless) are partial architectural decisions. They decide **how the system is shipped**, which is part of the structural picture. They become full architectures when they constrain the internal organisation:

- Microservices is an architecture because it decides "each service is independently deployable", which forces structural boundaries.
- Serverless is an architecture because it decides "code runs in short-lived managed compute", which forces certain structural choices.
- Generic statements like "we use Kubernetes" or "we deploy with Terraform" are not architectures; they are operational choices.

## What is and is not an architectural decision

Architectural decisions:

- "We will isolate the core from the database (hexagonal)."
- "We will use four concentric layers with inward dependencies (clean)."
- "Each service is independently deployable (microservices)."
- "Components communicate via events as the primary mode (EDA)."
- "Code runs in short-lived managed compute (serverless)."
- "We will not allow imports from delivery code into the core."

NOT architectural decisions:

- "We will use Postgres."
- "We will use Express vs Fastify."
- "We will use NestJS."
- "We will write tests with Jest."
- "We will format with Prettier."
- "We will name controllers `*Controller`."

The line: an architectural decision **constrains structure and dependencies**. A non-architectural decision selects a tool, library, or convention without changing the structural rules.

Some decisions sit on the boundary:

- Choosing an ORM is operational, but choosing whether the ORM types leak into the domain is architectural.
- Choosing a framework is operational, but choosing whether the framework couples the domain is architectural.
- Choosing a deployment platform is operational, but choosing whether each unit is independently deployable is architectural.

## Why this distinction matters

When the team conflates architecture with framework, design, patterns, or paradigms, the result is:

- "We use DDD" is offered as the answer to "what is the architecture?". The structural question is left unanswered.
- A new contributor sees `domain/`, `application/`, `infrastructure/` folders and assumes the project is hexagonal, when in fact the dependency rules are not enforced.
- A team adopts CQRS or Event Sourcing because it is "what mature DDD projects do", without realising those are independent patterns whose adoption requires a separate justification.
- A team migrates "from monolith to microservices" expecting to fix problems that were architectural in nature, not deployment-related.

Keeping architecture separate from its neighbours lets each decision be evaluated on its own terms.

## Output

When this skill is applied, the team can answer:

- What is the architecture of this system, in one sentence, in structural terms?
- What dependency rules does that architecture enforce?
- Which decisions in this codebase are architectural, and which are operational, design, patterns, or paradigm?
- Where is the architecture documented, and how is it enforced (review, tooling, tests)?

Without these answers, the architecture is implicit. Implicit architecture is whatever the team happens to do this week.
