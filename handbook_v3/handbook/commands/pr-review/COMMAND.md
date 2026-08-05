# pr-review

Evidence-based review of a pull request. Covers architecture, domain modeling, clean code, testing, QA, and risk. Produces an internal review record and a block ready to post as a review comment.

## When to use

When reviewing any pull request — including when no PR description exists.

## Activation

Load before starting the review:

- Role: `handbook/roles/software-architect.md` (axiom) + `handbook/roles/qa-engineer.md`
- Mandatory base for any review (see `routing-guide.md` "Default base for code review"):
  - `handbook/skills/qa-review/SKILL.md`
  - `handbook/skills/software-design/ddd/SKILL.md`
  - `handbook/skills/software-architecture/hexagonal-architecture/SKILL.md`
  - `handbook/skills/software-design/solid-principles/SKILL.md`
  - `handbook/skills/code-readability/SKILL.md`
- Always-on review aids:
  - `handbook/skills/impact-analysis/SKILL.md`
  - `handbook/skills/risk-classification/SKILL.md`
  - `handbook/skills/testing-strategy/SKILL.md`
- Compose additional skills the diff demands: `financial-domain-patterns`, `security-review`, `compliance-patterns`, the relevant language review, `database-design`, `api-design`, `observability`.

If the repo's architecture is evidently clean, onion, layered, or MVC instead of hexagonal, substitute the matching sub-skill. In the current Kintai ecosystem, only `finances/` is hexagonal; for any other repo, keep hexagonal as the lens — the absence of layer boundaries and bounded contexts is exactly what the review must surface.

## Flow

1. **Gather PR context**
   - Branch name, target branch, PR title, description (if any)
   - If no description: derive intent from title + commit messages + code diff
   - State the derived intent explicitly — this is the baseline for the entire review

2. **Fix the canonical diff** — the single source of truth of what is under review

   Run, in order:

   ```
   git fetch origin
   git log origin/<target>..HEAD --no-merges --pretty=format:'%H' > .pr-review-commits
   git diff origin/<target>...HEAD -- $(git diff --name-only origin/<target>...HEAD)
   ```

   The canonical diff is the union of patches produced by the non-merge commits in `.pr-review-commits`, restricted to the files they touched. Anything outside that diff does not exist for this review:

   - Lines that arrived via merge from the target branch are excluded by `--no-merges`.
   - Files modified only by merge commits are excluded.
   - Code that exists in the branch but was not written by the author in this PR is excluded.

   If the diff is empty, stop and say so.

3. **Scope rule for every dimension**

   Every finding must point to a `file:line` whose line appears as added (`+`) or modified in the canonical diff from step 2. If the line is not in the canonical diff, the finding is dropped — no exceptions, regardless of how relevant it seems.

   Reading the surrounding file is allowed only as context, to understand what the change touches. It is not allowed as the basis for a finding. Pre-existing code, code that arrived via merge, and code in unrelated files are out of scope.

   Before writing any finding, verify: "is this line in the canonical diff?" If not, discard it.

4. **Apply each review dimension in order**

   **a. Architecture** — `software-architecture` (drill into the sub-skill that matches the PR: `hexagonal-architecture`, `clean-architecture`, `onion-architecture`, etc.)
   - Layer boundaries respected (domain → application → infrastructure)?
   - Business logic in the right layer — not in controllers, handlers, or repositories?
   - Infrastructure details not leaking into domain or application code?
   - No unnecessary abstractions or missing ones that would clarify ownership?
   - Constraints of the chosen architecture preserved (do not mix vocabularies across sibling architectures)?

   **b. Domain modeling** — `software-design/ddd`
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

5. **Aggregate findings by severity**

   - 🚫 **Blocker** — must be resolved before merge (correctness error, security issue, boundary violation, breaking contract)
   - ⚠️ **Suggestion** — worth improving, not blocking; include a concrete alternative
   - 📝 **Observation** — informational, no action required

6. **Generate the PR comment** using the default human style below
   - Use the structured review matrix technique only when explicitly requested by the user

7. **Save the comment block** to `handbook/output/pr-reviews/comments/<ticket>-comment.md` and **show it in full in the message** — this is the primary deliverable of the review

8. **Save a record** to `handbook/output/pr-reviews/pr-<ticket>.md` following `handbook/governance.md`

## Default PR comment style

The default PR comment must read like a human colleague wrote it, not like a generated audit.

Use Francisco's natural review tone:
- Direct, conversational, and precise
- Firm when something blocks the PR, but without sounding ceremonial
- Written mostly in paragraphs
- Clear about why the point matters, not just what is wrong
- Practical about the fix when there is an obvious fix
- Comfortable saying "me surge una duda", "creo que aquí", "lo que me preocupa", "yo lo movería", or similar natural phrasing when it fits

Prefer this shape:

```markdown
## PR Review

<Opening paragraph with the PR context and what the change appears to do. If there is a blocker, say that there are changes requested without turning it into a table. If there are no blockers, say that the PR looks good and mention any small notes naturally.>

**<Short topic heading>**

<One or more paragraphs explaining the point. Include file or code references when useful. If the point is blocking, say it in the paragraph: "Esto sí lo bloquearía porque...". If it is only a suggestion, make that clear in the wording.>

**<Another short topic heading if needed>**

<More paragraphs.>

**Para cerrar**

<Short closing paragraph with the practical outcome: approve, request changes, or approve with minor comments. Mention risk only if it adds useful context.>
```

### Default style rules

- Do not group findings by fixed dimensions unless that grouping is useful for this PR.
- Do not include severity tables by default.
- Do not include a verdict table by default.
- Do not force every review dimension into the visible PR comment.
- Do not list "Architecture", "Domain modeling", "Code quality", "Testing", "QA", and "Risk" when some of them have nothing meaningful to say.
- Do not use long dash separators to express ideas. Use parentheses or a new sentence instead.
- Do not write titles like `Suggestion — reason` or `Blocker — reason`. Prefer `**Sobre <tema>**`, `**Duda sobre <tema>**`, or `**Bloqueante (motivo corto)**`.
- Avoid formulaic AI phrasing: "overall", "comprehensive", "robust", "it is worth noting", "ensure that", "this PR introduces". Use simpler, more natural wording.
- Keep emojis out of the default PR comment unless the user asks for them.
- Keep bullets rare. Use them only when they genuinely improve readability, for example a short list of concrete cases or checks.
- When a concrete fix is useful, write it as prose first. Add a code snippet only if it makes the fix clearer.
- If there are zero findings, skip the comment block entirely.
- **Never use "pediría", "lo señalaría", or "el autor".** The comment is pasted by Francisco in Bitbucket as his own text. Third-person references to "the author" or distanced forms like "lo señalaría" break the voice — Francisco is the one pasting, so he would be asking himself to confirm something. Use conditional forms ("habría que corregir esto antes de mergear") or direct questions ("¿es esto intencionado o entró de rebote?").
- **Mark blockers explicitly inside the paragraph.** When a finding blocks the merge, say so clearly in the paragraph text ("esto bloquea el merge porque...", "hay que resolver esto antes de aprobar"). Do not rely on section titles alone to convey severity.
- **Report blocker status to Francisco before showing the comment block.** Before pasting the full comment, tell Francisco plainly: what is blocking and what is not. One sentence is enough.

### Example default comment

```markdown
## PR Review

He estado revisando la PR y la intención parece clara: añadir `registered_letter` como opción de envío postal dentro del flujo de comunicaciones Collection. La parte de alta del provider está bien encaminada, pero tal como está ahora pediría cambios antes de mergear porque la opción queda visible sin activar toda la lógica que necesita detrás.

**Bloqueante (el listado de deudores no recibe el provider)**

En backend la validación de domicilio completo depende de que el listado reciba `sender_provider=registered_letter`. En esta PR el step de deudores sigue construyendo los params solo con `noa_type`, así que el usuario puede elegir burofax postal pero el listado se carga como si fuera un envío normal.

Esto lo bloquearía porque rompe el criterio principal de la historia: no basta con mostrar la opción, hay que deshabilitar o avisar correctamente cuando el deudor no tiene domicilio completo. Yo pasaría el provider seleccionado al construir los params del step y refrescaría el listado cuando cambie el provider, para evitar que quede una selección calculada con reglas anteriores.

**También falta el idioma para el nuevo provider**

`notificationSenderProviderMap` ya conoce `REGISTERED_LETTER`, pero `notificationLanguageMap` no. El resultado práctico es que el usuario puede seleccionar la vía postal y quedarse con el formulario inválido porque `language` sigue siendo obligatorio y no hay opción disponible.

Para el fix mínimo añadiría español para `REGISTERED_LETTER`. Si este mapa va a seguir creciendo, quizá conviene juntar provider e idiomas en una sola configuración, porque ahora es fácil crear combinaciones visibles pero no usables.

**Para cerrar**

Pediría cambios por esos dos puntos. El diff es pequeño, pero toca un flujo operativo sensible y ahora mismo puede dejar una opción visible que no se puede usar correctamente.
```

## Structured review matrix technique

Use this technique only when the user explicitly asks for the current structured format, a matrix by dimensions, a formal audit style, or a comment grouped by architecture/domain/code/testing/QA/risk.

This preserves the previous PR response format.

```
## PR Review

**Branch**: `<branch>`
**Target**: `<target>`
**Intent**: <derived or stated — one sentence>

---

### Resumen de situación

<one paragraph in natural language: what story or context the PR comes from, what business problem it solves, and what it implements specifically — written as if explaining to a colleague who hasn't seen the ticket, in the author's natural speaking style>

---

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
- Keep the review analysis separated by dimension internally, even when the posted comment is written in natural paragraphs
- Blockers require a concrete reason and a concrete fix
- Suggestions require a concrete alternative, not just a flag
- Do not flag style preferences as blockers
- Do not invent acceptance criteria not derivable from the evidence
- Always include enough situational context for the PR author and reviewers to understand the background without looking up the ticket
- Skip the comment block entirely when there are zero findings — a comment with nothing to say adds noise
- **Findings live inside the canonical diff or they do not exist.** The canonical diff is built from non-merge commits only (step 2). Any finding whose `file:line` is not present as added or modified in that diff is out of scope and must be dropped, even if the surrounding code looks problematic. Reading the full file is allowed for context; it is never the basis for a finding. This applies to pre-existing code, code that arrived via merge from the target branch, and unrelated files.

## Skills referenced

- `handbook/roles/software-architect.md` (axiom)
- `handbook/roles/qa-engineer.md`
- `handbook/skills/qa-review/SKILL.md`
- `handbook/skills/software-design/ddd/SKILL.md`
- `handbook/skills/software-architecture/hexagonal-architecture/SKILL.md`
- `handbook/skills/software-design/solid-principles/SKILL.md`
- `handbook/skills/code-readability/SKILL.md`
- `handbook/skills/impact-analysis/SKILL.md`
- `handbook/skills/risk-classification/SKILL.md`
- `handbook/skills/testing-strategy/SKILL.md`
