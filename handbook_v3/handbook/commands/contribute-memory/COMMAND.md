# contribute-memory

Adds a fact, convention, or gotcha to `handbook/memory/` in structured form. Enforces memory governance rules before writing anything.

## When to use

- After a session where something non-obvious was learned and should survive across sessions.
- After a post-mortem where a gotcha was confirmed.
- After a decision worth remembering across sessions.
- When the developer explicitly says "remember this" or "add this to memory".

Do not run this command proactively. Only when explicitly requested or when the session has produced clearly stable knowledge.

## Flow

1. Ask what should be added. If not already stated, ask the developer to describe it.
2. Classify the entry into one of:
   - **Project context** — a non-obvious fact about the project that is not inferrable from the code.
   - **Convention** — how something is done here.
   - **Gotcha** — a known trap with a known workaround.
   - **Reference** — where something lives (a file, a document, a decision).
   - **Work in progress** — a task or pending item that may continue across sessions.
3. Apply memory governance rules from `handbook/governance.md`:
   - Is it already covered by a skill? If yes, point to the skill instead of duplicating.
   - For stable knowledge: can it be expressed as a one-line pointer? If not, it probably belongs in a skill.
   - For work in progress or pending items: use the work-item template and place in `tasks/` or `pending/` subfolder. For exploratory approximations not tied to a ticket, use `explorations/`.
4. For stable knowledge: draft the entry in pointer format — one line, concrete, no explanation.
5. Identify where in `handbook/memory/` it belongs. List existing files and subfolders first.
6. Apply the change to the correct file or subfolder.
7. Announce what was added and where.

## Examples

✅ Good entry: `Login flow delegates MFA verification to VerifyMfaUseCase — see skills/software-design/ddd`
❌ Bad entry: A three-paragraph explanation of how MFA works in the system (that belongs in a skill)

## Rules

- Never add without explicit request.
- Never add PII, tokens, or secrets.
- If the entry would duplicate a skill, propose updating the skill instead.

## Skills referenced

- `handbook/governance.md` — memory management rules applied during this flow.
