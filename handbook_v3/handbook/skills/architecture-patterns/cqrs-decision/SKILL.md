# CQRS Decision

## Purpose

Evaluate whether command/query separation is useful for the current case.

## Responsibilities

- Identify command-side and query-side concerns
- Detect whether reads and writes have different models, constraints, or performance needs
- Evaluate whether CQRS reduces complexity or adds unnecessary ceremony
- Identify consistency implications
- Identify testing and operational impact
- Recommend whether to use CQRS, partial CQRS, or a simpler approach

## Instructions

- Start from the use case, not from the pattern
- Identify what changes state
- Identify what reads state
- Check whether reads and writes need different models
- Check whether query performance, projections, or reporting needs justify separation
- Check whether consistency can be eventual or must be immediate
- Prefer simple command/query separation before introducing heavier CQRS infrastructure

## Heuristics

CQRS is more likely useful when:

- Read and write models have clearly different needs
- Queries require optimized projections
- Reporting/search/read performance is important
- Writes enforce domain invariants
- Reads must combine data from multiple sources
- Eventual consistency is acceptable and useful

CQRS is less likely useful when:

- Reads and writes use the same simple model
- The use case is mostly CRUD
- Separation would only add files without reducing complexity
- Consistency requirements are immediate and simple
- There is no meaningful domain behavior behind the command

## Rules

- Do not apply CQRS just because DDD is present
- Prefer CQRS when it clarifies intent or reduces complexity
- Avoid CQRS when it only adds ceremony
- Make consistency trade-offs explicit
- Keep command-side behavior focused on state changes
- Keep query-side behavior focused on reading and presentation needs

## Activity Traceability

🔧 Loading skill: `cqrs-decision`