# 07. Review Checklist

Operational checklist for reviewing an MVC codebase, after the cross-architecture review (`software-architecture/references/08-review-checklist.md`) has confirmed that MVC is in use.

## Step 1: Where is MVC operating?

1. Is MVC the system-wide architecture (small UI app with little business logic outside)?
2. Or is MVC a UI structure inside a larger architecture (hexagonal, clean, onion, layered)?

**Stop condition.** If neither answer is clear, MVC is being applied accidentally. Pause and clarify the architectural context first.

## Step 2: Are the three roles identified?

1. Are controllers identified and named?
2. Are views identified and named?
3. Are models identified and named?
4. In a larger architecture, is the MVC model distinguished from the domain model?

**Stop condition.** If the roles are blurred (a class plays multiple roles, or "everything is a controller"), stop and report.

## Step 3: Are controllers thin?

1. Do controllers parse input, call into the model or application core, build a view-model, select a view? And little else?
2. Are there controllers with business rules (computations, validations beyond format, decisions on whether the operation is allowed)?
3. Are there controllers that access the database directly?
4. Are there controllers longer than a screen?

## Step 4: Are views dumb?

1. Do views render only?
2. Are there views with conditionals based on business rules (not formatting)?
3. Are there views that call services or stores?
4. Are there views that mutate state?

## Step 5: Are models in their proper place?

1. In a small MVC-only app, does the model hold data and behaviour?
2. In a larger architecture, is the MVC model a view-model / DTO, distinct from the domain model?
3. Are domain types leaking to the view (templates that read aggregate methods directly, JSON responses shaped like aggregates)?

## Step 6: Is the application core respected (in a larger architecture)?

1. Do controllers call the application core (use case, service) for any non-trivial operation?
2. Are there controllers that bypass the application core and access repositories directly?
3. Are controllers building the view-model from the application core's result, not from the aggregate directly?

**Stop condition.** None. Findings here are violations of the larger architecture and may indicate that MVC has degenerated into "code in controllers".

## Step 7: Variant consistency

1. Is the MVC variant in use (web MVC, classical, MVC inside hexagonal, MVP, MVVM) consistent across the codebase?
2. Are mixing of variants happening (some files are MVC, others MVP, others MVVM)?

## Summary

After all steps, produce:

- **Top three findings** by impact. Skipping the application core, fat controllers, and domain-as-view-model are the most damaging.
- **Quick wins.** Move logic from a controller to a use case; build a view-model where one is missing; remove a service call from a view.
- **Backlog.** Refactor a god controller; extract use cases; add view-model layer.
- **Confirmed strengths.** Parts that are sound.

## Forbidden conclusions

- "It is fine because we use a popular MVC framework."
- "It is fine because we have controllers, views, and models named accordingly."
- "MVC is the architecture, so the backend architecture is fine."

MVC is a UI structure. It does not validate the rest of the system.
