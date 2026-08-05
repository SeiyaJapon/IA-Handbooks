# Impact Analysis Skill

## When to use

The user asked to estimate the structural impact of a change, diff, or proposed migration: which files, modules, contexts, services are affected; transversal vs local.

## When not to use

- Risk classification: `risk-classification` (this skill provides the input; risk classification consumes it).
- Architectural review of the change itself: `software-architecture`.

## Inputs to inspect first

- The diff or list of changed files.
- Affected modules, contexts, services.
- Sensitive areas: auth, money, contracts, shared libraries, configuration, persistence, messaging.

## How to work

1. List the changed files.
2. Identify affected modules/contexts/services.
3. Walk for transversal signals (cross-context, cross-service, contracts, shared foundations).
4. Flag sensitive areas.
5. Output a concise impact report.

## Output

A structured impact summary: files, modules, contexts, services, sensitive areas, transversal vs local classification.

## Escalation

- Risk classification: `risk-classification`.
- Architecture review: `software-architecture`.
- Integration impact: `integration-analysis`.

---

## Purpose

Analyze a technical case, diff, or list of changed files to estimate structural impact.

## Responsibilities

- Identify affected files when available
- Estimate affected modules, services, or bounded contexts
- Estimate affected layers
- Detect sensitive areas
- Detect whether the case is local or transversal
- Support risk estimation with structural signals

## Instructions

- Start from the provided input only
- If changed files are provided, list them
- Use file paths, names, visible code signals, and stated requirements to estimate modules
- Use file paths, names, visible code signals, and stated requirements to estimate layers
- Look for signals that suggest propagation across services, repositories, bounded contexts, or shared foundations
- Look for sensitive areas such as auth, money, contracts, shared code, infrastructure, configuration, messaging, or persistence boundaries
- Identify whether the case appears local or transversal
- Provide the analysis in a way that can be used by risk evaluation and final reporting

## Heuristics

Treat as stronger impact signals when the case affects:

- Shared libraries or common modules
- Public contracts, schemas, DTOs, APIs, or events
- Auth, permissions, billing, payments, or money-related flows
- Infrastructure configuration, deployment definitions, or messaging topology
- Base abstractions, framework wrappers, or cross-cutting components
- Multiple services, repositories, or bounded contexts
- Entry points plus internal domain logic in the same case

Treat as likely local when the case appears limited to:

- A single module
- A single bounded area
- A narrow internal implementation detail
- A non-shared component without contract changes

## Rules

- Base the analysis on visible evidence
- Prefer simple heuristics over speculative reasoning
- Do not redesign the system
- Propose solutions only if the user asks or if they are necessary to explain the impact
- Do not make final business decisions
- Do not assume hidden dependencies without signals
- Keep the analysis brief and structured

## Activity Traceability

🔧 Loading skill: `impact-analysis`