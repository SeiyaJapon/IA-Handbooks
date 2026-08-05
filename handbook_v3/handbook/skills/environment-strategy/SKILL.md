# Environment Strategy Skill

## When to use

The user asked about environment management, promotion strategy, deployment pipelines, ephemeral environments, staging/preprod/prod design.

## When not to use

- Provisioning IaC for one environment: `terraform-review`.
- Cloud architecture: `aws-infrastructure`.
- Specific service operational concerns: `long-running-services-architecture` or `serverless-architecture`.

## Inputs to inspect first

- Current environments and how they are deployed.
- Branch strategy and pipeline design.
- Artifact strategy (build once, promote many; rebuild per environment).
- DB and infra coupling per environment.
- Multi-repo coordination if applicable.

## How to work

1. Map the current state.
2. Identify the active pain (overwriting, manual coordination, no preprod, drift).
3. Propose a model that addresses the pain at minimum cost.
4. Surface trade-offs.

## Output

A model recommendation tied to the project's specific constraints; not a generic best-practice list.

## Escalation

- Provisioning IaC: `terraform-review`.
- Cloud architecture: `aws-infrastructure`.

---

## Purpose

Evaluate, design, or review decisions about environment management, promotion pipelines, and deployment strategy in the context of Kintai's architecture and current maturity.

## Context

Kintai's specific constraints that any environment strategy must account for:

- **API as gateway and DB mediator**: the DB is only accessible through `api/`. Any ephemeral environment that needs DB access must provision a paired API instance.
- **Multi-repo with lambdas**: 40+ repos. Lambdas are invoked by `api/` directly. A change in a lambda repo may not be visible in `api/`'s tests unless the lambda is stubbed or co-deployed.
- **Current state (as-is)**: no `staging` branch, shared staging deployed by Slack coordination ("cojo staging"), master deploys directly to production. No preprod. No artifact versioning.
- **Local harness debt**: historically unstable (LocalStack version pinning, init script timing, Node 14 EOL, AWS SDK v2). Being resolved as of May 2026 (MiniStack migration in `api/`).
- **Terraform per repo**: each service has its own Terraform state in S3. No single Terraform that provisions the full platform. `infrastructure/` and `terraform-modules/` are shared foundations.

## The Four Models (P1 → P4)

### P1 — Minimum structured (low maturity)
Three fixed environments: `staging`, `preprod`, `prod`.  
Three branches: `staging`, `preprod`, `master` (each with its own pipeline).  
Key value: eliminates overwriting, formalizes a preprod ring.  
Key limitation: staging is still shared; PRs are duplicated; multi-repo coordination is still manual.  
Effort: low–medium.

### P2 — Progressive promotion / build once promote many (medium maturity)
One PR per feature (`feature/*` → `staging`).  
Build an immutable artifact (SHA-tagged image + Lambda version).  
Promote the **same artifact** through staging → preprod → prod without rebuilding.  
Key value: what you tested is what you deployed.  
Key addition: smoke/e2e gates per ring, release manifest for multi-repo coherence.  
Effort: medium.

### P3 — Ephemeral environments per PR (high maturity)
Each PR gets its own isolated environment: URL, DB, secrets, resources.  
Destroyed automatically on PR close or TTL expiry.  
Key value: real parallelism, no staging contention.  
Key challenges: DB provisioning per PR (snapshot/restore or schema per PR), secret scoping per PR (`pr/<id>/...`), multi-repo manifest with `depends-on`, observability per PR.  
Effort: high.

### P4 — Strategic hybrid (very high maturity)
P3 + release train + canary + mandatory feature flags.  
Deploys are decoupled from releases.  
Rollback via weights + flags, not redeployment.  
Effort: very high (sociotechnical).

## Incremental Roadmap

**Fase 0 (1–2 weeks)** — Prerequisites  
- Stable local harness (deterministic tests, lambda stubs, credential management)  
- Naming conventions for environments, secrets, and artifacts  
- CI runs integration tests reliably against a local emulator (MiniStack/Floci)  
- Production-quality Dockerfile: multi-stage, non-EOL base image, `npm ci --production`

**Fase 1 — P1 (2–4 weeks)**  
- `staging` and `preprod` branches + pipelines  
- Secrets by environment (SSM/Secrets Manager prefix per env)  
- Migration validation in pipeline (plan + apply + verify)  
- "Redeploy previous" job  
- Basic runbooks

**Fase 2 — P2 (4–8 weeks)**  
- SHA-tagged images in ECR (not mutable `{env}:{env}` tags)  
- Promote artifact, not rebuild  
- Smoke tests per ring as promotion gate  
- First release manifest (multi-repo version lockfile)  
- IAM separation: build permissions vs promote permissions

**Fase 3 — P3 (8–16 weeks)**  
- Idempotent IaC per PR + TTL + automated destroy  
- DB strategy: snapshot/restore as baseline, schema-per-PR as optimization  
- Secret scoping per PR + revocation on close  
- Contract tests + `depends-on` manifest  
- Observability with `env_id=pr-{number}` tagging

**Fase 4 — P4 (16+ weeks)**  
- Feature flags with owner + expiry as standard practice  
- Release train (weekly cadence → increase)  
- Canary with traffic splitting + automatic rollback on guardrail breach

## Instructions

- Start from the current maturity level before proposing anything
- Map any proposal to a specific Phase and model — never jump phases without justification
- Identify which Kintai constraints the proposal must account for (DB mediator, multi-repo, lambda coupling)
- Check whether the proposal closes a Fase 0 gap or advances a higher model
- For multi-repo decisions: assess whether the change applies only to `api/` or needs coordination across other repos
- For Terraform changes: verify they are scoped to the correct environment target (local emulator vs real AWS)
- Flag MiniStack/Floci as a Fase 0 tool — it does not advance the model beyond local+CI reliability on its own

## Heuristics

Treat as stronger signals that the current model is insufficient when:
- The team uses Slack or verbal coordination to "reserve" a shared environment
- Tests that passed locally fail in CI or staging regularly
- A deployment to staging overwrites another team member's in-progress work
- Rolling back requires manual coordination rather than a single pipeline action
- A new developer cannot run the full test suite locally without tribal knowledge

Treat as premature advancement when:
- Proposing P3/P4 without stable CI (Fase 0 not done)
- Proposing ephemeral environments without a DB strategy
- Proposing a release train without feature flags
- Proposing canary without observability per version

## Rules

- Do not propose a model without naming its prerequisites
- Do not treat MiniStack/Floci adoption as completing the environment model — it is Fase 0 infrastructure
- Do not recommend P3 or P4 without an explicit plan for DB isolation and secret scoping
- Explain the adoption cost alongside the value
- For multi-repo changes: a solution that only works in `api/` and leaves other repos misaligned is a partial solution

## Activity Traceability

🔧 Loading skill: `environment-strategy`
