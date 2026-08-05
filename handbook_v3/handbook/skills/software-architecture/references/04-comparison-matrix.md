# 04. Comparison Matrix

A cross-architecture comparison. For each pair of axes (architecture × force), this document records when each architecture fits, when it does not, and what it composes with.

Use this when shortlisting candidates after `02-how-to-choose-an-architecture.md` has identified the forces.

## Architectures by family

For convenience, the architectures are referenced here using short codes:

- **HEX**: Hexagonal (Cockburn)
- **CLN**: Clean (Martin)
- **ONI**: Onion (Palermo)
- **LAY**: Layered (classical)
- **MVC**: Model-View-Controller
- **MSV**: Microservices
- **SVL**: Serverless
- **EDA**: Event-Driven Architecture

## Fit by domain complexity

| Domain complexity | HEX | CLN | ONI | LAY | MVC | MSV | SVL | EDA |
|---|---|---|---|---|---|---|---|---|
| Trivial CRUD | Overkill | Overkill | Overkill | Fits | Fits in UI | Overkill | Fits | Overkill |
| Moderate | Fits | Fits | Fits | Fits | Inside delivery | Fits | Fits | Fits |
| Rich | Fits | Fits | Fits | Possible, weaker boundaries | Inside delivery only | Fits | Fits | Fits |

Notes:
- Trivial CRUD on hexagonal/clean/onion is "overkill" because the ceremony costs more than it saves.
- Rich domains on layered work, but the data layer tends to leak upward over time.

## Fit by expected lifetime

| Lifetime | HEX | CLN | ONI | LAY | MVC | MSV | SVL | EDA |
|---|---|---|---|---|---|---|---|---|
| Throwaway | Overkill | Overkill | Overkill | Fits | Fits in UI | No | Fits | No |
| Mid-term | Fits | Fits | Fits | Fits | Inside delivery | Fits if multiple teams | Fits | Fits if async-heavy |
| Long-term | Fits | Fits | Fits | Risky | Inside delivery only | Fits | Fits | Fits |

Notes:
- Throwaway projects do not justify hexagonal/clean/onion ceremony; layered or framework-default is enough.
- Long-term projects on layered tend to migrate to hexagonal/clean over time.

## Fit by team topology

| Team topology | HEX | CLN | ONI | LAY | MVC | MSV | SVL | EDA |
|---|---|---|---|---|---|---|---|---|
| One person | Heavy | Heavy | Heavy | Fits | Fits | No | Fits | No |
| Small single team | Fits | Fits | Fits | Fits | Inside delivery | Overkill | Fits | If async-heavy |
| Multiple teams, single context | Fits | Fits | Fits | Fits | Inside delivery | Possible | Fits | Possible |
| Multiple teams, multiple contexts | Fits | Fits | Fits | Risky | Inside delivery | Fits | Fits | Fits |

Notes:
- One-person projects rarely justify the inward-dependency family.
- Multiple teams with multiple contexts almost always benefit from microservices or modular monoliths with hexagonal-per-module.

## Fit by change rate

| Change rate | HEX | CLN | ONI | LAY | MVC | MSV | SVL | EDA |
|---|---|---|---|---|---|---|---|---|
| Slow | Possible | Possible | Possible | Fits | Inside delivery | Overkill | Fits | Overkill |
| Steady | Fits | Fits | Fits | Fits | Inside delivery | Fits | Fits | Fits |
| Asymmetric (parts change at very different rates) | Fits | Fits | Fits | Risky | Inside delivery | Strong fit | Fits | Strong fit |

Notes:
- Asymmetric change rates push for microservices or EDA, because synchronous coupling slows fast parts to the speed of the slow.

## Fit by scale

| Scale | HEX | CLN | ONI | LAY | MVC | MSV | SVL | EDA |
|---|---|---|---|---|---|---|---|---|
| Small | Fits | Fits | Fits | Fits | Fits | Overkill | Fits | Possible |
| Medium | Fits | Fits | Fits | Fits | Inside delivery | Fits | Fits | Fits |
| Large | Fits | Fits | Fits | Strained | Inside delivery only | Fits | Fits | Fits |

Notes:
- Scale alone does not pick an architecture, but at large scale layered (alone) becomes strained because of monolithic deployment characteristics.

## Composition

Which architectures compose with which? Yes = natural fit. Limited = possible with caveats. No = conflict.

| Compose | HEX | CLN | ONI | LAY | MVC | MSV | SVL | EDA | DDD | CQRS | ES |
|---|---|---|---|---|---|---|---|---|---|---|---|
| HEX | self | No (sibling) | No (sibling) | No (sibling) | Inside delivery | Yes | Yes | Yes | Yes | Yes | Yes |
| CLN | No | self | No | No | Inside delivery | Yes | Yes | Yes | Yes | Yes | Yes |
| ONI | No | No | self | No | Inside delivery | Yes | Yes | Yes | Yes | Yes | Yes |
| LAY | No | No | No | self | Inside delivery | Limited | Yes | Limited | Limited | Yes | Limited |
| MVC | Inside delivery | Inside delivery | Inside delivery | Inside delivery | self | Inside delivery | Inside delivery | Inside delivery | n/a | n/a | n/a |
| MSV | Yes | Yes | Yes | Limited | Inside delivery | self | Yes | Yes | Yes | Yes | Yes |
| SVL | Yes | Yes | Yes | Yes | Inside delivery | Yes | self | Yes | Yes | Yes | Yes |
| EDA | Yes | Yes | Yes | Limited | Inside delivery | Yes | Yes | self | Yes | Yes | Yes |
| DDD | Yes | Yes | Yes | Limited | n/a | Yes | Yes | Yes | self | Yes | Yes |
| CQRS | Yes | Yes | Yes | Yes | n/a | Yes | Yes | Yes | Yes | self | Yes |
| ES | Yes | Yes | Yes | Limited | n/a | Yes | Yes | Yes | Yes | Yes | self |

Read it as: "row composes with column?". DDD, CQRS, ES are added at the end because they are routinely confused with architectures even though they are design / patterns; this matrix shows they compose with all of them.

## Ceremony cost

How much does the architecture cost to set up and maintain?

| Architecture | Setup cost | Maintenance cost | Notes |
|---|---|---|---|
| HEX | Medium | Low once set | Pays back in long-lived rich-domain systems |
| CLN | Medium | Low once set | Same as HEX |
| ONI | Medium | Low once set | Same as HEX |
| LAY | Low | Low (small systems) / High (long-term, large) | Cost grows over time |
| MVC | Low | Low | Inside one delivery adapter, light touch |
| MSV | High | High | Operational cost: deployment, observability, contracts |
| SVL | Low setup, medium operational | Medium | Vendor lock-in, cold starts, debugging |
| EDA | Medium-high | Medium-high | Broker operations, idempotency, ordering |

## When NOT to pick (rule-out conditions)

These are unambiguous "do not pick" signals.

- **HEX/CLN/ONI** when the domain is trivial CRUD and the lifetime is short.
- **LAY** when the domain is rich and the lifetime is long.
- **MVC as the system-wide architecture** when there is real business logic outside the UI.
- **MSV** when there is one team and one context.
- **MSV with a shared database**: it is a monolith with extra steps.
- **SVL** for long-running processes or latency-critical paths sensitive to cold starts.
- **EDA** when interactions are naturally synchronous and the team has no broker-operations maturity.

## Anti-patterns in comparison

- **Picking on lines of code.** Architecture cost is not measured in setup lines; it is measured in friction over the system's lifetime.
- **Comparing only two options.** The shortlist should usually have two or three. Comparing only one against itself is no comparison.
- **Comparing without forces.** Without naming the forces, the comparison reduces to taste.
- **Treating composition as conflict.** Hexagonal + microservices is one decision (the system shape) with two components, not two competing architectures.
- **Ignoring brownfield.** Comparison for an existing system must include the migration path, not only the destination architecture.

## Output

When this matrix is applied, the team can produce:

- A shortlist of candidate architectures (typically two or three).
- For each, the fit against the project's specific forces.
- For each, the composition decisions implied (e.g. picking microservices implies an internal architecture per service).
- A picked option with the reason written down.

The matrix is not the answer; it is the input to the decision.
