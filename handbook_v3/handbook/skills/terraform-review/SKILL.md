# Terraform Review Skill

Review Infrastructure as Code (Terraform / Terragrunt / OpenTofu) for **correctness, security, maintainability, and operational safety**. Operational: inspect module boundaries, environments, providers, state, and blast radius before suggesting changes.

## When to use

Use this skill when:

- The user asks for a Terraform review of a module, environment, or PR.
- Infrastructure is being added, changed, or migrated.
- A `plan` output looks risky and the team wants a second read.
- State management, drift, or provider versions are surfaced.
- Module reuse or environment promotion is being designed.

## When not to use

Do not use this skill when:

- The question is **about cloud architecture** at the design level (VPC topology, ECS vs Lambda, multi-AZ strategy). Use `aws-infrastructure`.
- The question is **about a specific AWS service's correctness in code**. Use `aws-review`.
- The question is **about deployment pipelines and environment strategy** more broadly. Use `environment-strategy`.
- The question is **about secrets management or compliance posture**. Use `security-review` or `compliance-patterns`.
- The question is **about Docker images or container builds**. Use `docker-review`.

## Inputs to inspect first

Before recommending Terraform changes, inspect:

- **Module boundaries.** What modules exist? What is reusable, what is environment-specific?
- **Environments.** dev, staging, preprod, prod. How are they parameterised? Where do they diverge?
- **Providers and versions.** `required_providers`, `required_version`. Pinned, range, latest? Provider source registry.
- **State assumptions.** Local state? Remote backend (S3, Terraform Cloud, GCS)? State locking? Workspace strategy?
- **Variable flow.** `variables.tf`, `terraform.tfvars`, env-specific tfvars, `TF_VAR_*` env, secret variables.
- **Outputs.** What does each module export? Who consumes the outputs?
- **Secrets handling.** Where do secrets live? In code? Env? Secrets manager? Are they marked `sensitive = true`?
- **Drift risk.** Are there resources mutated outside Terraform? Are there `lifecycle` ignore_changes blocks?
- **Plan/apply workflow.** CI-driven? Manual? Pipeline-managed state? Who approves plans?
- **Remote state interactions.** `terraform_remote_state` data sources; cross-stack dependencies.
- **Blast radius.** What does an `apply` of this module touch? Single resource? Whole VPC? Production data?
- **Terraform version per repo.** Different repos in this monorepo may use different Terraform versions; the version is stamped in the state on every write.

## Hard rules (about agent behaviour)

- **Inspect module boundaries, environments, and state before reading code.**
- **Identify the blast radius before suggesting changes.** A change in a shared module is not the same as a change in one environment.
- **Do not modify Terraform unless the user asked.**
- **If modifying, prefer the smallest change.** Terraform changes have real-world consequences.
- **Run `terraform fmt`, `terraform validate`, `terraform plan`** when available and safe (read-only operations).
- **Never run `terraform apply` without explicit user authorisation.**
- **Use the project's pinned Terraform version.** Terraform stamps the version into state on every write; using a newer CLI against older state breaks pipelines.
- **Distinguish reading state from writing state.** `state pull`, `state list`, `output` are read-safe; `state rm`, `state push`, `import`, `apply` are write operations.
- **Group findings by severity and blast radius.**

## Terraform-specific concerns

- **Provider pinning.** Floating providers cause silent drift on init.
- **State locking.** Concurrent applies without locks corrupt state.
- **Sensitive variables.** Marked `sensitive`, sourced from secret managers, never committed.
- **Lifecycle blocks.** `prevent_destroy`, `ignore_changes`, `create_before_destroy` used deliberately.
- **Module versioning.** Reusable modules pinned by version; not tracking `main`.
- **Resource taint and replacement.** `replace_triggered_by`, taint, `terraform apply -replace`.
- **Drift between state and reality.** Resources mutated outside Terraform.
- **Cross-stack dependencies.** `terraform_remote_state` couples stacks; consider data sources or explicit outputs.
- **Backend migration.** Moving state between backends is a one-way operation if not careful.
- **Workspace strategy.** Per-environment workspaces, per-environment directories, or per-environment tfvars; consistency matters.
- **Tagging conventions.** Consistent tags for cost, ownership, environment.
- **Cost-impacting resources.** NAT gateways, large instances, premium tiers; surface them.

## How to work

1. **Read the module structure**, including environment overlays and shared modules.
2. **Identify the blast radius** of the change.
3. **Read the providers and version constraints.**
4. **Read the state backend** configuration.
5. **Run `validate` and `plan`** if available and safe.
6. **Walk the Terraform-specific concerns** above.
7. **Group findings**: blockers, security issues, defects, nits.
8. **Suggest the smallest change** aligned with the project's conventions.
9. **Escalate** when the issue is architecture, security, or environment strategy.

## Output

Return findings as:

- **Blockers:** state corruption risks, unauthenticated public exposure, secret leakage, untested production-touching changes.
- **Security issues:** overly permissive IAM, public S3, open security groups.
- **Defects:** unpinned providers, missing locks, undocumented modules, drift risk.
- **Nits:** style, naming, tagging consistency.

Cite file paths and resource addresses. State the blast radius.

## Escalation

- Cloud architecture decisions (VPC topology, service choice, multi-AZ): `aws-infrastructure`.
- A specific AWS service's correctness: `aws-review`.
- Deployment pipelines, environment promotion strategy: `environment-strategy`.
- Secrets management posture, compliance: `security-review`, `compliance-patterns`.
- Docker images and container builds: `docker-review`.

## Anti-patterns the agent should reject

- Running `terraform apply` without explicit authorisation.
- Suggesting changes without identifying blast radius.
- Recommending state surgery (`state rm`, `state push`) lightly.
- Using a different Terraform version than the pipeline's.
- Generic "use modules" advice without inspecting the existing module structure.

## Original purpose (legacy reference)

Review Infrastructure as Code definitions for correctness, security, maintainability, and operational safety.

## Responsibilities

- Review resource definitions for correctness and completeness
- Detect hardcoded secrets, credentials, or environment-specific values
- Evaluate state management approach and backend configuration
- Assess module structure and reuse
- Detect drift risks from resource renames or removals
- Review variable, output, and sensitive value handling
- Evaluate lifecycle rules and destroy protection on critical resources
- Assess provider version pinning

## Instructions

- Start from changed `.tf` or `.tfvars` files
- Check for hardcoded credentials, account IDs, or region strings
- Verify sensitive outputs are marked as `sensitive = true`
- Check backend configuration: remote state, locking, encryption
- Evaluate whether modules are appropriately scoped and reusable
- Check `count` vs `for_each` — prefer `for_each` for maps and sets
- Look for resource renames that would trigger implicit destroy/recreate
- Check provider and module version pins

## Heuristics

Treat as stronger concerns when:

- Secrets or tokens in `.tfvars`, variable defaults, or inline values
- Missing `prevent_destroy` on critical resources (databases, state buckets)
- Resource rename without `moved` block or lifecycle alias — triggers destroy
- State stored locally without locking
- Sensitive outputs not marked as sensitive
- Unpinned provider versions in production configurations
- IAM resources with `*` on action or resource (see `aws-review`)

Treat as acceptable when:

- Hardcoded region when single-region deployment is intentional and documented
- Missing `prevent_destroy` in non-production environments
- Loose version pins in development modules

## Rules

- Never approve secrets in Terraform files
- Flag any resource change that triggers an implicit destroy/recreate
- Remote state with locking is required for shared environments
- Check the plan interpretation — not just the code — when available

## Activity Traceability

🔧 Loading skill: `terraform-review`
