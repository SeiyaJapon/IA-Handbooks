# Prompt Engineering Skill

Treat prompts as **versioned, testable, reviewable artefacts**: instruction design, structure, few-shot examples, chain-of-thought, structured output, robustness to unexpected input. Prompts are part of the system, not free text.

## When to use

Use this skill when:

- A prompt is being designed, reviewed, or refined.
- The output is inconsistent and the question is "is this a prompt problem or a model problem?".
- A team is moving prompts out of inline strings into versioned, testable artefacts.
- A structured output (JSON, schema) is being defined.
- A prompt is being hardened against malformed or adversarial input.

## When not to use

Do not use this skill when:

- The work is **integrating the LLM** (clients, retries, fallback). Use `ai-integration`.
- The work is **autonomous control flow with tools**. Use `ai-agents`.
- The work is **retrieval design** (chunking, embeddings, ranking). Use `rag-design`. Prompt engineering is downstream of retrieval design in a RAG system.
- The work is **production operations** (eval harness, drift, cost). Use `llm-ops`. Prompt engineering produces the prompts; llm-ops verifies them in production.
- The user wants prompts optimised before evaluation criteria exist. Define the eval first; prompt engineering follows.

## Inputs to inspect first

Before reviewing or designing prompts, inspect:

- **The task.** What does the prompt accomplish? Stated unambiguously, in one sentence.
- **The expected output.** Free text, structured (JSON), classified, ranked, summarised?
- **Existing prompts** in the codebase. Are they versioned? Tested? Or inline strings?
- **Eval criteria.** What does "good output" mean for this task? Is there a golden dataset or rubric?
- **Input distribution.** What real inputs does the prompt see? Edge cases?
- **Model and version.** Some techniques are model-specific; instructions for one model may underperform on another.
- **Prompt injection threat surface.** Where does user input enter the prompt?

If eval criteria are missing, prompt iteration is opinion. Define eval first.

## Hard rules

- **Prompts are artefacts.** Versioned in the repo, reviewed in PRs, tested with a golden dataset. Not inline strings that drift silently.
- **Define evaluation before optimisation.** Without a measure, "better prompt" is taste.
- **Ambiguous instructions produce inconsistent outputs.** Rewrite the prompt before debugging the model.
- **Structured output requires an explicit schema and at least one example.**
- **System and user prompts are structurally separate.** User input does not reach the system prompt unsanitised; instructions and untrusted content are visually and structurally distinct.
- **Prompt injection is a real threat.** User input, retrieved documents, tool outputs can carry adversarial instructions. Validate; sanitise; defend output.
- **Test edge cases.** Empty input, adversarial input, malformed input, very long input, very short input.
- **Pin the model.** Prompts are tuned for specific models; rotating without re-evaluation is a regression risk.

## Concerns this skill covers

- Instruction clarity and unambiguity.
- Few-shot example selection and format.
- Chain-of-thought and reasoning elicitation, when it helps.
- Structured output (JSON schemas, format constraints, validation downstream).
- System vs user prompt boundary.
- Prompt injection mitigations.
- Context window efficiency: removing low-value boilerplate.
- Robustness to edge cases.
- Versioning and review workflow.
- Per-task prompt evaluation.

## How to work

1. **Confirm the trigger** is prompt-level, not retrieval-level, integration-level, or operations-level.
2. **Inspect eval criteria.** If absent, define them before iterating.
3. **Inspect the existing prompt** for ambiguity, format, examples, system/user separation, robustness.
4. **Identify the active force.** Why is the current prompt failing? Inconsistent output, hallucination, format violation, injection?
5. **Recommend a minimal change.** A clearer instruction, a structured-output schema, a few-shot example, a refusal pattern.
6. **Verify with eval.** A new prompt is not better until the eval says so.
7. **Document the prompt and its version.** Future contributors should understand why this prompt exists in this shape.

## Output

Return findings as:

- **Concrete prompt issues:** ambiguity, missing format, weak examples, injection exposure, drift between system and user.
- **Recommended changes:** smallest edit that addresses the force; ideally accompanied by an eval delta.
- **Architectural escalations:** when the problem is not the prompt (retrieval, model choice, integration, autonomy).

## Escalation

- The integration around the prompt (clients, retries, streaming): `ai-integration`.
- Autonomous control flow consumes the prompt: `ai-agents`.
- Retrieval feeds the prompt: `rag-design`.
- Production eval, drift, cost, model rotation: `llm-ops`.
- Whether the task should use AI at all: `ai-systems`.
- Privacy or compliance of prompt content: `security-review`, `compliance-patterns`.

## Operational checks (legacy, kept as a checklist)

When reviewing a prompt, walk these signals:

- Instruction that can be interpreted in two or more valid ways.
- No output format specified for structured tasks; model invents its own.
- Few-shot examples all from the same narrow case; poor generalisation.
- Chain-of-thought requested but not anchored; model reasons then ignores.
- System prompt that can be overridden by user input ("ignore previous instructions").
- No example output for complex JSON schema; model hallucinates fields.
- Token budget wasted on boilerplate that does not affect output.
- Prompt that only works with one specific model version (and rotation is not planned).
