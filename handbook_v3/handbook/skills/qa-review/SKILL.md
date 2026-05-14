# QA Review

## Purpose

Evaluate whether a feature or change behaves correctly from a functional and product perspective.

## Responsibilities

- Verify that observable behavior matches the stated or derived intent
- Identify edge cases not handled from a user or product perspective
- Evaluate implicit acceptance criteria when no description exists
- Detect user-facing error states that are missing or incorrect
- Flag scenarios the author likely did not test manually
- Distinguish happy-path-only implementations from complete ones

## Instructions

- If no PR description exists: derive intent from title, commit messages, and code structure — state the derived intent explicitly before reviewing
- Think as the first user of the feature, not as the author
- Check boundary conditions: empty states, maximum values, invalid inputs, concurrent actions
- Evaluate error paths visible to the user: what does the UI show, what does the API return, what happens on failure
- Verify that the happy path is not the only path covered
- Do not repeat architectural or code quality findings — those belong in other skills

## Heuristics

Treat as stronger QA concerns when:

- Only the happy path is implemented with no error handling
- Empty state is not handled (empty list, no results, null entity)
- User-visible errors are swallowed or shown as generic messages
- Async operations have no loading, error, or timeout handling
- Form submissions allow duplicate submissions or have no feedback
- Derived intent does not match what the code actually does
- A feature is configurable but the configuration edge cases are untested

Treat as acceptable when:

- Edge cases are handled at a higher level (middleware, framework, shared boundary)
- The scope is explicitly a partial implementation with a known follow-up
- Error handling is delegated to an existing boundary that already covers it

## Rules

- Derive intent from evidence (title, commits, code) — do not invent acceptance criteria
- Separate product concerns from technical concerns — this skill covers product behavior
- Do not flag architectural or code quality issues here
- Flag explicitly when intent cannot be derived confidently
- A missing edge case is only a blocker if it affects a realistic user scenario

## Activity Traceability

🔧 Loading skill: `qa-review`
