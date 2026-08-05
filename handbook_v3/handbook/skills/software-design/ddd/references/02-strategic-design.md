# 02. Strategic Design

Strategic design is the part of DDD that decides **what the system is about** before deciding how the code is shaped. It produces three artefacts:

- A list of **subdomains** with their type (core, supporting, generic).
- A list of **bounded contexts** that implement those subdomains.
- A **context map** describing how the contexts relate.

This document covers the first two steps and the discovery process. Bounded contexts have their own reference (`03-bounded-contexts.md`). Context maps have their own reference (`04-context-mapping.md`).

## Domain, subdomain, bounded context

These three terms are constantly confused. They are not interchangeable.

- **Domain.** The entire problem space the business operates in. There is one domain per business.
- **Subdomain.** A coherent slice of the domain, defined by the business itself, not by the technology. Subdomains are conceptual: they exist before any software is written and would still exist if the company wrote everything by hand.
- **Bounded context.** An explicit implementation boundary inside which a model and its language are consistent. A context belongs to the implementation; a subdomain belongs to the business.

The mapping between subdomains and bounded contexts is **not** one-to-one by default. It is a decision:

- One subdomain, one bounded context: the simple, common case.
- One subdomain, several bounded contexts: when the subdomain is large enough that splitting clarifies the model (different teams, different lifecycles).
- One bounded context, several subdomains: when two subdomains share a model so closely that splitting would create artificial duplication.

The subdomain list is discovered. The bounded context list is decided. Both must be explicit before any tactical work.

## Business capabilities

A useful starting point for discovering subdomains is the list of **business capabilities**: the things the business does, named in business terms.

Examples in a risk management product:

- Onboarding clients.
- Pricing receivables.
- Simulating risk scenarios.
- Resolving products against a catalog.
- Mapping payment methods.
- Authenticating users.

Each capability is a candidate subdomain. The candidate becomes a subdomain when it has a coherent vocabulary, a clear owner in the business, and rules that distinguish it from neighbouring capabilities.

## Subdomain types

Not every subdomain deserves the same investment. Classify each subdomain into one of three types:

- **Core domain.** Where the business creates competitive advantage. The reason the product exists. Receives the deepest modelling effort, the strongest team, and the most domain-aligned code. Investment: high. DDD ceremony: full.
- **Supporting subdomain.** Necessary for the core to work but not differentiating. Built in-house because no off-the-shelf component fits, but with less ceremony than the core. Investment: medium. DDD ceremony: partial, focused on what supports the core.
- **Generic subdomain.** Solved problems that everybody has: authentication, billing, mailing, audit logging. Buy or delegate to an external provider when possible. Internal code is a thin adapter, not a model. Investment: low. DDD ceremony: minimal or none.

The classification matters because it tells you where to spend modelling time. Spending core-level effort on a generic subdomain (writing aggregates and events for `auth`) is waste. Spending generic-level effort on the core is malpractice.

The classification also drives **buy vs build**: generic subdomains are usually bought; supporting are usually built simply; core is always built and modelled deeply.

## Strategic discovery process

The output of strategic design is not produced from a chair. It comes from talking to domain experts and looking at the existing system or business with deliberate questions.

A practical sequence:

1. **List capabilities.** What does the business actually do? Aim for ten to thirty entries, named in business terms (verbs or noun phrases the business uses).
2. **Group by coherence.** Capabilities that share vocabulary, rules, or stakeholders cluster into the same subdomain. Capabilities whose grouping is forced (because someone decided "we need a `users` subdomain") are a smell.
3. **Classify subdomains.** Core, supporting, generic. The classification is a business judgment, not a technical one. Ask the business: "if this disappeared, would we still have a product?" If yes, it is not core. "Would competitors do this differently?" If no, it is generic.
4. **Look for hidden cores.** A subdomain that the team treats as supporting but where the business says "this is the thing we are best at" is a hidden core. Re-prioritise.
5. **Surface vocabulary tensions.** When the same word means different things in two places, mark it: it is a sign of a future bounded context split.
6. **Decide bounded contexts.** For each subdomain, decide whether one context suffices or whether the subdomain needs splitting. The default is one context per subdomain; split only with a reason.
7. **Draw the context map.** See `04-context-mapping.md`.

## Vocabulary as a discovery tool

The fastest way to find a missing subdomain or a misplaced one is to listen for **the same word used with different meanings**.

If "client" in the sales conversation means a registered company with a contract, and "client" in the platform conversation means a row in a table that may or may not be a real customer, those are two concepts under one word. Either rename one of them in the language (and propagate to code), or split into two contexts so each context can own its meaning.

The opposite case also matters: **two words that mean the same thing**. If the business says "scenario" and the engineering team says "calculation", and they refer to the same object, the language is divided. Pick one and propagate.

Vocabulary cleanup is not cosmetic. It is the cheapest way to discover boundaries.

## Anti-patterns in strategic design

- **Treating departments as subdomains.** A subdomain is a coherent region of meaning, not a reporting line in the org chart. Sometimes they coincide; often they do not.
- **Treating tables as subdomains.** A `users` table does not imply a `users` subdomain. The subdomain is the capability ("authenticating users", "managing user profiles"), not the storage.
- **Skipping subdomain classification.** Without core/supporting/generic, the team will spend effort uniformly. That guarantees underinvestment in the core and overinvestment in the generic.
- **Letting the framework decide the boundaries.** If subdomains are whatever the framework's module loader picks up, they are not subdomains; they are folders.
- **Discovering subdomains alone.** Strategic discovery without domain experts produces a model that fits the developer's mental model of the business, not the business itself.

## Output of strategic design

When strategic design is complete enough to start tactical work, the team has:

- A list of subdomains, each labelled core / supporting / generic, with a one-line description in business terms.
- A list of bounded contexts, each tied to one or more subdomains, with a one-line description of its responsibility.
- An initial context map (even rough) showing the relationships between contexts.
- A short list of language decisions (which word is canonical for which concept, in which context).

This output is the input to tactical design. Without it, tactical design is guessing.
