# Code Readability

## Purpose

Write code that reads as clearly as it thinks. A reader should understand what code does without having to simulate its execution.

## Core Principles

### Name by intent, not by implementation

A function name describes what it accomplishes, not how it does it.

```typescript
// ❌ describes the mechanism
function iterateContactsAndCompareCustomId(...) {}

// ✅ describes the intent
function matchByCustomId(...) {}
```

If you cannot name a block without using "and", it is doing more than one thing.

### If a block has a natural name, it is a function

When reading a block of code and a name comes to mind immediately, extract it. The name is the signal. Inline logic that could have a name is logic that forces the reader to interpret instead of read.

### An orchestrating function reads like prose

A function that coordinates other functions should contain almost no logic of its own — only a sequence of named steps. The reader should understand the full flow without entering any of the called functions.

```typescript
// ✅ reads top to bottom, no interpretation needed
async function findOrCreateContactId(http, input) {
  if (!hasIdentifiers(input)) return noResult();

  const match = await lookupContact(http, input);
  if (match) return resultFromMatch(match);

  const contactId = await createContact(http, input);
  return { contactId, needsAccountingConfig: true };
}
```

### Structural repetition signals missing abstraction

Three loops over the same collection, each doing a slightly different check, is not three solutions — it is one solution written three times. Extract the invariant, name the variation.

### Separate files only when a function has autonomous value

Prefer named module-level functions in the same file over separate files when:
- The function depends on internal utilities not worth exporting
- It has no meaningful use outside the current module

Create a separate file when:
- The function is a self-contained pure transformation (e.g. `buildContactPayload`, `buildInvoicePayload`)
- It would be imported by more than one module
- It represents a distinct named concept in the domain

Private class methods follow the same logic: use them when the function is tightly bound to instance state and has no value outside the class.

## Process

1. Make it work
2. Make it readable — name the blocks
3. Make it expressive — the orchestrator reads as intent, the helpers as implementation

Never skip step 2 on the way to step 3.

## Red Flags

- A function longer than can be read without scrolling
- A loop body with more than one level of nesting
- The same guard (`if (!isRecord) continue`, `if (!id) continue`) repeated in multiple loops
- A variable named `result`, `data`, `item`, or `tmp`
- A comment that explains *what* the code does (the code should do that itself)

## Activity Traceability

🔎 Loading skill: `code-readability`
