# Prompt Engineering

## Purpose

Review and improve prompt design for clarity, robustness, and output quality — covering instruction design, few-shot examples, chain-of-thought, and structured output.

## Responsibilities

- Evaluate instruction clarity and unambiguity
- Assess few-shot example selection and format
- Review chain-of-thought and reasoning elicitation techniques
- Evaluate structured output design (JSON schemas, format constraints)
- Assess system prompt vs user prompt boundary
- Review prompt robustness to unexpected input
- Evaluate context window usage efficiency
- Detect prompt fragility and edge cases

## Instructions

- Check the instruction: is the task unambiguous? could a human misinterpret it?
- Check the output format: is the expected format explicitly described? is there an example?
- Check few-shot examples: do they cover the range of expected inputs? are they consistent?
- Check chain-of-thought: is the model asked to reason before answering? does it need to be?
- Check the system prompt: does it set role, constraints, and output format clearly?
- Check for prompt injection vectors: can user input subvert the system prompt?
- Check context efficiency: is redundant or low-value content consuming token budget?
- Check robustness: what does the prompt produce on empty input, adversarial input, or edge cases?

## Heuristics

Treat as stronger concerns when:

- Instruction that can be interpreted in two or more valid ways
- No output format specified for structured tasks — model invents its own format
- Few-shot examples that are all from the same narrow case — poor generalization
- Chain-of-thought requested but not anchored — model reasons and then ignores its own reasoning
- System prompt that can be overridden by user input (`ignore previous instructions`)
- No example output for complex JSON schema — model hallucinates field names
- Token budget wasted on boilerplate that doesn't affect output
- Prompt that only works with one specific model version

Treat as acceptable when:

- No few-shot examples for genuinely simple, unambiguous tasks
- Loose output format for exploratory or creative tasks where variation is desirable

## Rules

- Ambiguous instructions produce inconsistent outputs — rewrite before debugging the model
- Structured output requires an explicit schema and at least one example
- Prompt injection must be considered whenever user input reaches the prompt
- Test prompts against edge cases before production deployment

## Activity Traceability

🔧 Loading skill: `prompt-engineering`
