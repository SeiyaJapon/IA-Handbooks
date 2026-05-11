# pr-review

Evidence-based review of a pull request. Covers architecture, domain modeling, clean code, testing, QA, and risk. Produces a structured report and a block ready to post as a review comment.

## When to use

When reviewing any pull request — including when no PR description exists.

## Activation

Load before starting the review:

- Role: `handbook/roles/software-architect.md`
- Skills:
  - `handbook/skills/architecture-review/SKILL.md`
  - `handbook/skills/hexagonal-boundaries/SKILL.md`
  - `handbook/skills/domain-modeling/SKILL.md`
  - `handbook/skills/code-readability/SKILL.md`
  - `handbook/skills/impact-analysis/SKILL.md`
  - `handbook/skills/risk-classification/SKILL.md`
  - `handbook/skills/testing-strategy/SKILL.md`
  - `handbook/skills/qa-review/SKILL.md`

## Flow

1. **Gather PR context**
   - Branch name, target branch, PR title, description (if any)
   - If no description: derive intent from title + commit messages + code diff
   - State the derived intent explicitly — this is the baseline for the entire review

2. **Get the full diff**
   - Local: `git diff origin/<target>...HEAD`
   - Remote branch: checkout the branch first, then diff against base
   - If the diff is empty, stop and say so

3. **Apply each review dimension in order**

   **a. Architecture** — `architecture-review` + `hexagonal-boundaries`
   - Layer boundaries respected (domain → application → infrastructure)?
   - Business logic in the right layer — not in controllers, handlers, or repositories?
   - Infrastructure details not leaking into domain or application code?
   - No unnecessary abstractions or missing ones that would clarify ownership?
   - Clean Architecture and hexagonal constraints preserved?

   **b. Domain modeling** — `domain-modeling`
   - Data structures model domain concepts, not just persistence shapes?
   - Entities, value objects, and aggregates used where appropriate?
   - Invariants placed close to the model that owns them?
   - Domain language consistent — no infrastructure names driving domain language?
   - Anemic models or misplaced business logic?

   **c. Code quality** — `code-readability`
   - Names express intent, not mechanics — no abbreviations, no generic names?
   - Orchestrating functions read like prose — no hidden logic?
   - Structural repetition signals a missing abstraction?
   - Blocks with a natural name are extracted to named functions?
   - No comments explaining what the code does (the code should do that itself)?

   **d. Testing** — `testing-strategy`
   - Business behavior and invariants covered?
   - Test level appropriate to what is being tested (unit / integration / e2e)?
   - Assertions specific — no `toBeTruthy` or `toBeDefined` where a shape could be asserted?
   - Critical paths tested, not just implementation details?

   **e. QA** — `qa-review`
   - Behavior matches derived or stated intent?
   - Edge cases handled: empty states, boundary values, invalid inputs?
   - Error paths visible to the user handled (not just happy path)?
   - Async operations have loading, error, and timeout handling where applicable?

   **f. Risk** — `risk-classification` + `impact-analysis`
   - Sensitive areas touched (auth, money, contracts, shared code)?
   - Cross-service or cross-context impact?
   - Breaking changes in APIs, schemas, or events?
   - Reversibility of the change?

4. **Aggregate findings by severity**

   - 🚫 **Blocker** — must be resolved before merge (correctness error, security issue, boundary violation, breaking contract)
   - ⚠️ **Suggestion** — worth improving, not blocking; include a concrete alternative
   - 📝 **Observation** — informational, no action required

5. **Generate the output block** (see template below)

6. **Save a record** to `handbook/output/audits/pr-<branch-or-ticket>.md` following `handbook/governance.md`

## Output template

```
## PR Review

**Branch**: `<branch>`
**Target**: `<target>`
**Intent**: <derived or stated — one sentence>

### Architecture & clean architecture
<findings, or ✅ No issues>

### Domain modeling & data structures
<findings, or ✅ No issues>

### Code quality
<findings, or ✅ No issues>

### Testing
<findings, or ✅ No issues>

### QA
<findings, or ✅ No issues>

### Risk
<findings, or ✅ No issues>

---

**Verdict**

| Severity | Count |
|---|---|
| 🚫 Blockers | N |
| ⚠️ Suggestions | N |
| 📝 Observations | N |

**Decision**: ✅ Approve / 🔄 Request changes
```

## Rules

- State derived intent before reviewing — reviewing against the wrong intent produces noise
- Separate findings by dimension — do not mix architectural and QA concerns in the same note
- Blockers require a concrete reason and a concrete fix
- Suggestions require a concrete alternative, not just a flag
- Do not flag style preferences as blockers
- Do not invent acceptance criteria not derivable from the evidence

## Skills referenced

- `handbook/roles/software-architect.md`
- `handbook/skills/architecture-review/SKILL.md`
- `handbook/skills/hexagonal-boundaries/SKILL.md`
- `handbook/skills/domain-modeling/SKILL.md`
- `handbook/skills/code-readability/SKILL.md`
- `handbook/skills/impact-analysis/SKILL.md`
- `handbook/skills/risk-classification/SKILL.md`
- `handbook/skills/testing-strategy/SKILL.md`
- `handbook/skills/qa-review/SKILL.md`
