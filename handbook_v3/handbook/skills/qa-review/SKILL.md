# QA Review Skill

## When to use

The user asked for a PR/code review and the diff spans multiple concerns. This skill is the integrating router for general PR reviews: it composes other review skills as the diff demands.

## When not to use

- The user named a single language or technology. Use the corresponding review skill directly.
- The user asked for an architecture review. Use `software-architecture`.
- The user asked for a security review. Use `security-review`.
- The user asked for testing strategy guidance. Use `testing-strategy`.

## Inputs to inspect first

- The diff: files, additions, deletions, areas affected.
- The PR description (if any): intent, scope, link to ticket.
- Test coverage on the changed paths.
- Build/test/lint/typecheck status if available.

## Mandatory composition for any code review

A review run through this skill **always** loads the following five, regardless of the diff or the request phrasing:

- `qa-review` (this skill)
- `software-design/ddd` — bounded contexts, aggregates, value objects, ubiquitous language. Required even if the repo does not currently apply DDD; the absence is itself a finding.
- `software-architecture/hexagonal-architecture` — ports, adapters, layer boundaries, dependency direction. Required even if the repo has no architecture; the absence is itself a finding. The ecosystem direction is hexagonal + DDD where missing.
- `software-design/solid-principles` — SRP, OCP, LSP, ISP, DIP.
- `code-readability` — names, function size, prose-like flow.

Additional skills compose on top per the diff: `financial-domain-patterns`, `security-review`, `compliance-patterns`, the relevant language review (`typescript-review`, `python-review`, etc.), `database-design`, `api-design`, `testing-strategy`, `observability`. Load only those whose concerns the diff actually touches.

A review that does not consider bounded contexts, layer boundaries, dependency direction, SOLID, or readability is incomplete by definition.

If the repo evidently uses a different sibling architecture (clean, onion, layered, MVC), substitute `hexagonal-architecture` for the matching one. In the current Kintai ecosystem, only `finances/` is hexagonal; everywhere else, hexagonal is the target lens, and loading it is what surfaces the gap.

## How to work

1. Read the diff and infer the change's intent.
2. Load the mandatory base five above. Add the additional skills the diff demands.
3. Walk findings by concern, then by severity.
4. Group: blockers, defects, nits, suggestions.
5. Do not modify code unless asked.

## Output

A grouped review:

- **Blockers** that must be fixed before merge.
- **Defects** that should be fixed but do not block.
- **Nits** small style/preference issues.
- **Suggestions** opportunities for follow-up work, not requested in this review.

## Escalation

When the diff is dominantly one concern, escalate to that concern's primary skill rather than running it second-hand:

- Architectural change: `software-architecture` and the relevant sub-skill.
- Design change inside a module: `software-design`.
- AI integration: the relevant `ai-systems` sub-skill.
- Security: `security-review`.
- Specific language: the corresponding language review skill.

---

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
