# self-review

Structured review of your own diff before opening a PR. Forces the developer to understand every change and flags weak tests before the code reaches reviewers.

## When to use

Before opening any pull request. Not optional — if a diff exists, this runs.

## Flow

1. Get the full diff against the base branch (`git diff origin/<base>...HEAD`).
2. If there are no changes, stop and say so.
3. For each changed file:
   - Show the diff for that file.
   - Ask the developer: "What does this change do and why?"
   - If the answer is generic (fewer than 8 meaningful words, or phrases like "fix", "adjustment", "ticket stuff"): push for specificity. Do not accept vague answers.
   - If the developer cannot explain it: mark as **Not-reviewed**.
4. For test files (`*.test.*`, `*.spec.*`): apply `handbook/skills/testing-strategy/SKILL.md` and flag weak assertions (e.g. `toBeTruthy`, `toBeDefined` where a specific shape could be asserted).
5. Mark each file with one of:
   - **Reviewed-and-understood** — developer explained it clearly.
   - **Reviewed-with-doubts** — explanation was partial or the file has issues flagged.
   - **Not-reviewed** — developer could not explain it. Blocks the PR by convention.
6. Count changed files. If more than ~30 (excluding lockfiles, generated files, snapshots): flag and suggest splitting the PR.
7. Generate the output block below, ready to paste into the PR description.

## Output template

```
## self-review output

**Diff stats**: N files, M lines changed (excl. generated).

| File | Status | Notes |
|---|---|---|
| `path/to/file.ts` | ✅ Reviewed-and-understood | ... |
| `path/to/other.ts` | ⚠️ Reviewed-with-doubts | ... |
| `path/to/test.ts` | ⚠️ Reviewed-with-doubts | Weak assertions on line 42 |

**Test audit**:
- `test.ts:42` — `toBeTruthy` — prefer asserting specific shape.

**Required actions before PR**:
- [ ] ...

**Diff size**: N files. ✅ ok / ⚠️ consider splitting.
```

## Rules

- Do not mark anything as reviewed if the developer has not explained it.
- Do not accept "I don't have time" as a reason to skip. If the developer is genuinely blocked on time, the PR goes with `Not-reviewed` marked — the reviewer will reject it.
- Trivial changes (whitespace, rename) can be marked green with a short note, but must be stated explicitly.

## Skills referenced

- `handbook/skills/testing-strategy/SKILL.md` — for test file audit.
