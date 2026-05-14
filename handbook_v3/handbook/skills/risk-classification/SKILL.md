# Risk Classification

## Purpose

Estimate the structural, operational, or delivery risk of a technical case.

## Responsibilities

- Review the available analysis or current case context
- Evaluate the likelihood of breakage, propagation, hidden impact, or long-term coupling
- Consider the effect of sensitive areas
- Classify the risk as low, medium, or high
- Provide a short justification for the classification

## Instructions

- Start from the available evidence
- Use affected files, modules, layers, scope, contracts, dependencies, and sensitive areas as main signals when available
- Treat transversal cases as stronger risk signals than local cases
- Treat contracts, auth, money, shared foundations, infrastructure, configuration, persistence, or messaging as higher risk signals
- Consider cross-repository or cross-service propagation as a significant risk signal
- Consider uncertainty itself as a risk signal when the impact is unclear
- Produce a simple and consistent classification
- Provide a short reason that explains the main signals behind the result

## Heuristics

Classify as low risk when the case appears:

- Local
- Limited to a single module or bounded area
- Limited to a narrow implementation detail
- Free of strong sensitive-area signals
- Free of contract or shared-foundation changes

Classify as medium risk when the case appears:

- Moderately broad
- Spread across more than one area but still understandable
- Related to a sensitive area without strong propagation signals
- Unclear enough to require attention, but not escalation

Classify as high risk when the case appears:

- Transversal
- Spread across several modules, services, repositories, or bounded contexts
- Affecting contracts, APIs, schemas, events, auth, money, shared foundations, infrastructure, configuration, persistence, or messaging
- Likely to cause hidden propagation or difficult-to-see breakage
- Unclear in impact while touching sensitive areas
- Strong enough to require explicit attention

## Rules

- Base the classification on available signals
- Keep the classification simple and consistent
- Do not invent impact without evidence
- Do not replace architectural analysis
- Do not make final business decisions
- Be conservative when several high-impact signals appear together
- State the main reason for the risk level

## Activity Traceability

🔧 Loading skill: `risk-classification`