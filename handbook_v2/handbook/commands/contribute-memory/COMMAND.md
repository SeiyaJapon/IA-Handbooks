# contribute-memory

Adds a fact, convention, or gotcha to `handbook/memory/` in structured form. Enforces memory governance rules before writing anything.

## When to use

- After a session where something non-obvious was learned and should survive across sessions.
- After a post-mortem where a gotcha was confirmed.
- After a decision that does not warrant a full ADR but is worth remembering.
- When the developer explicitly says "remember this" or "add this to memory".

Do not run this command proactively. Only when explicitly requested or when the session has produced clearly stable knowledge.

## Flow

1. Ask what should be added. If not already stated, ask the developer to describe it.
2. Classify the entry into one of:
   - **Project context** — a non-obvious fact about the project that is not inferrable from the code.
   - **Convention** — how something is done here.
   - **Gotcha** — a known trap with a known workaround.
   - **Reference** — where something lives (a file, a document, a decision).
3. Apply memory governance rules from `handbook/governance.md`:
   - Is this stable knowledge? If it is ephemeral (state of a task, current sprint), do not add it.
   - Is it already covered by a skill? If yes, point to the skill instead of duplicating.
   - Can it be expressed as a one-line pointer? If not, it probably belongs in a skill.
4. Draft the entry in pointer format: one line, concrete, no explanation.
5. Identify which file in `handbook/memory/` it belongs in. List existing files first.
6. Apply the change to the correct memory file.
7. Announce what was added and where.

## Examples

✅ Good entry: `Login flow delegates MFA verification to VerifyMfaUseCase — see skills/domain-modeling`
❌ Bad entry: A three-paragraph explanation of how MFA works in the system (that belongs in a skill)

## Rules

- Never add without explicit request.
- Never add PII, tokens, or secrets.
- Never add task state or sprint state.
- If the entry would duplicate a skill, propose updating the skill instead.

## Skills referenced

- `handbook/governance.md` — memory management rules applied during this flow.
