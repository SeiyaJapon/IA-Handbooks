# Report Output Skill

## When to use

The user asked for a structured technical report (sections, headings, evidence). Use at the end of the reasoning phase to package conclusions.

## When not to use

- Prose-first writing for mixed audiences: `prose-report`.
- Status views and panoramic summaries: `status-presentation`.

## How to work

1. Identify the structure expected (architectural review, security findings, decision critique, etc.).
2. Use the project's templates if any (`handbook/templates/`).
3. Group by severity or by section as the structure demands.
4. Avoid filler.

## Output

A structured report ready to share, with links/citations to evidence.

## Escalation

Use the appropriate output skill if the form does not match.

---

## Purpose

Present technical work in a clear, brief, and structured way.

## Responsibilities

- Convert analysis, decisions, reviews, or proposals into a useful output
- Keep the result short and easy to scan
- Preserve the meaning of the source analysis
- Make relevant signals visible
- Emphasize risks, trade-offs, decisions, and next steps when relevant

## Instructions

- Use the available analysis or current case context
- Preserve the requested output structure when one exists
- Keep field names stable when producing structured records
- Keep explanations brief
- Surface the most relevant signals clearly
- Make high-risk, transversal, or architecturally relevant points easy to notice
- Avoid unnecessary wording
- Do not add conclusions that are not supported by the analysis

## Output Types

Use the most appropriate output type for the case.

### Case Summary

Use for normal technical answers.

Recommended fields:

- context
- analysis
- recommendation
- trade_offs
- risks
- next_steps

### Work Record

Use for handbook/output/ records.

Recommended fields:

- case_name
- current_state
- open_questions
- decisions
- risks
- history

### Architecture Decision Candidate

Use when the result may become stable memory.

Recommended fields:

- decision
- context
- options_considered
- trade_offs
- consequences
- memory_candidate

### Review Output

Use for code, design, or architecture reviews.

Recommended fields:

- summary
- findings
- risks
- recommendations
- required_changes
- optional_improvements

## Rules

- Do not change the meaning of the analysis
- Do not add unsupported conclusions
- Prefer clarity over completeness
- Keep the output brief unless detail is explicitly useful
- Preserve the requested or relevant structure
- Highlight high-risk, transversal, or unresolved items clearly
- List findings by importance when possible

## Activity Traceability

🔧 Loading skill: `report-output`