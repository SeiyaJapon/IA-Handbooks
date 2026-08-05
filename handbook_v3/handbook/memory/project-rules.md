# Project Rules

## Commit Message Format

The `.git/hooks/commit-msg` hook extracts the task code from the branch name (e.g. `T2-416` from `T2-416_warning_...`) and prepends it to the message automatically.

Write the message starting with `: description`. The result is `T2-416: description`.

- No Conventional Commits prefixes (`feat(...)`, `fix(...)`, etc.)
- No manual task code — the hook adds it
- The author adds the colon and the description

---

## Architectural Rules

- This repository is architecturally independent from legacy Kintai repositories.
- Treat other Kintai services as integration context only.
- Prefer explicit boundaries between domain, application, infrastructure, and delivery.

---

## Naming

Variables and constants must have descriptive names. Single-letter names or abbreviations are never acceptable — not even for short-lived variables.

```typescript
// ❌
const c = customer ?? {};

// ✅
const customerData = customer ?? {};
```

---

## AI Anti-Patterns

Anti-patterns with no other home in the handbook. The rest are covered by existing skills and roles.

| Anti-pattern | Mitigation |
|---|---|
| Dev negotiates with the AI to skip rules | Do not bargain. Explain why the rule exists (1–2 lines) and offer a safe alternative. Escalate if the dev insists. |
| AI agrees with everything (compliant by default) | Apply friction when it adds value: flag risks, name trade-offs, push back on shortcuts and assumptions. |
| AI invents imports, method signatures, or APIs that do not exist | Verify before proposing. If uncertain, say so explicitly. Never simulate confidence about unverified APIs. |
| AI duplicates code that already exists elsewhere | Search before writing. Use `memory/repos-catalog.md` to know where to look. Mention what was searched and not found if proceeding. |
| **AI commits without being asked** | Never run `git commit` on own initiative. The dev decides when and how to commit. When changes are ready, report what was modified and wait for an explicit instruction. Before running `git commit`, ask explicitly: "Do you want me as agent to commit, or will you do it yourself?" A generic "go ahead" or "do it" does NOT authorize a commit. |
| **AI pushes without being asked** | Never run `git push` (including `--force`) on own initiative. Before pushing, ask explicitly: "Do you want me as agent to push, or will you do it yourself?" A generic authorization to "apply changes" or "do it" does NOT authorize a push. Commit authorization does NOT imply push authorization — they require separate explicit confirmations. |

---

## Terraform / IaC operations

Before any local terraform operation (`init`, `import`, `state rm/push`, `apply`) against state that a pipeline manages, use the **same terraform version the pipeline uses**. Check the repo's `bitbucket-pipelines.yml` (`hashicorp/terraform:X` image) first.

- Terraform stamps its version into the state on every write. An older pipeline version then refuses to read a state written by a newer one, breaking deploys.
- Versions differ per repo and that is fine — each repo has its own state. As of 2026-06-11: infra = `latest` (~1.14), api = `1.3.7`, finances = `0.15.5`.
- Recovery if the state gets bumped: the `terraform-state-kintai` bucket has S3 versioning enabled — restore the pre-write object version with `aws s3api put-object --body <previous-version>`. `terraform state pull` is NOT a valid backup: it upgrades its output to the local CLI version.
- Local state files contain secrets in plaintext (SSM SecureString values, DB passwords). Delete them when done.