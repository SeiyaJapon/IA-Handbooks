# Terraform Review

## Purpose

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
