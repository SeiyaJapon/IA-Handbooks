# Architecture Decisions

When to write an ADR: when a decision made in a session affects more than one module, closes a valid alternative, or has long-term consequences. Write it before closing the session. Brief is fine — context + decision + consequences is enough.

```
## ADR-XXX — <Title>

Status: Accepted | Superseded by ADR-YYY

Context:
<Why this decision was needed. What the constraints were.>

Decision:
<What was decided.>

Consequences:
- <What this enables or requires.>
- <What this rules out.>
```

---

## ADR-001 — Repository architecture is independent from legacy Kintai services

Status: Accepted

Context:
Legacy Kintai repositories use different architectural standards. Without an explicit boundary, there is a risk of copying legacy patterns into new code.

Decision:
This repository defines its own architectural standards.

Consequences:
- Legacy Kintai repositories must not be copied as architectural examples.
- Integration with other services must be explicit and treated as external context.