# Prose Report Skill

## When to use

The user asked for a report whose primary form is prose (paragraphs, narrative), often for mixed audiences (engineering + product). Compose at the end of reasoning, not during.

## When not to use

- Structured technical reports with sections: `report-output`.
- Status / panoramic summaries: `status-presentation`.

## How to work

1. Identify the audience and the decision the report supports.
2. Write a tight, prose-first artefact: lead with the conclusion, then the reasoning, then the supporting detail.
3. Avoid generic best-practice filler.

## Output

A prose document, ready to share.

## Escalation

Use other output skills if the form is wrong.

---

## Purpose

Write technical reports that are accurate for engineering and readable for product — without sacrificing rigor for one or accessibility for the other.

## Responsibilities

- Produce reports that a product manager or business stakeholder can follow without a technical background
- Ensure the same report gives an engineer enough precision to act on it: file paths, code snippets, failure modes, root causes
- Distinguish between what is dangerous and what is merely imperfect — and name both clearly
- Preserve the severity of findings without softening them in the name of accessibility
- Avoid false comfort: a risk is what it is, regardless of how politely it is described

## Instructions

### On structure

- Lead with what matters most. The reader's first paragraph should answer "what is the problem and why does it matter", not "what is the scope of this document".
- Reserve tables and bullet lists for cases where the comparison, enumeration, or lookup value is genuine — a list of files, a comparison of options, a checklist. Never use them to avoid writing a sentence.
- Use section headers when a document is long enough to need navigation. Do not use them as a substitute for transitions.
- Code blocks belong in the report when the code itself is the evidence. A prose claim about a bug is weaker than showing the buggy line.

### On tone

- Write as if explaining to a smart colleague who is not a specialist in this area. That means no jargon without explanation, but also no condescension.
- Severity must come through in the words, not just in a label. "This is a risk" is weak. "This produces incorrect totals silently, with no error thrown" is precise.
- When something is wrong for a fintech or any regulated context, say so explicitly. Do not let regulatory or correctness requirements hide behind "recommendation".

### On dual audiences

- After establishing the technical root cause, explain the business consequence in plain language. Not every reader needs to understand IEEE 754; every reader does need to understand "charges can be off by one or two cents, invisibly, on every transaction".
- When showing code, include a one-line explanation of what it demonstrates. Engineers will read the code; product readers will read the explanation and trust the code is there.
- Avoid writing two separate sections for "technical" and "non-technical" audiences — that creates redundancy and signals that the author couldn't reconcile the two. A well-written paragraph does both at once.

### On completeness vs. precision

- Prefer precision over completeness. A report that covers three problems with depth and accuracy is better than one that mentions ten problems superficially.
- If a finding is uncertain, say so — and explain what would need to be true for it to become a confirmed risk. Do not omit it; do not state it as fact.

## Rules

- Do not soften risk classifications to make the report more palatable
- Do not add findings not supported by evidence
- Do not use lists where prose is clearer
- Always name the consequence, not just the cause — "this can produce wrong results" needs a "which means customers could be overbilled or underbilled"
- Code shown must be actual code from the codebase, not paraphrased or reconstructed

## Activity Traceability

🔧 Loading skill: `prose-report`
