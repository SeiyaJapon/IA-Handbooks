# Docker Review Skill

## When to use

The user asked for a Docker review (Dockerfiles, image strategy, multi-stage builds, docker-compose, container security, base images) of a file or PR.

## When not to use

- Container orchestration at platform level (ECS, Kubernetes, Cloud Run): `aws-infrastructure` or the relevant platform skill.
- IaC for infrastructure: `terraform-review`.
- Long-running service runtime concerns: `long-running-services-architecture`.
- Serverless: `serverless-architecture`.

## Inputs to inspect first

- `Dockerfile`(s), `docker-compose.yml`, `.dockerignore`, multi-stage structure.
- Base image: official, Alpine, distroless, scratch. Tag pinned or floating?
- Build context, COPY patterns, ARG/ENV usage, secrets handling.
- Final image size, layer count, cache friendliness.
- User: root vs non-root.
- Healthcheck, ENTRYPOINT vs CMD, signal handling.
- Compose: networks, volumes, environment, depends_on (and limits).

## How to work

1. Inspect Dockerfile(s) and compose file.
2. Run `docker build` if practical and safe; `hadolint` if available.
3. Walk Docker-specific concerns: pinned base, minimal layers, root user, secrets in build args, multi-stage to drop build deps, healthcheck, signal handling.
4. Group findings by severity. Do not modify files unless asked.

## Output

Findings cited by file and line, grouped: blockers (running as root, secrets baked in, latest tag in production), defects (no multi-stage, missing healthcheck, large image), nits (layer order for cache, .dockerignore gaps).

## Escalation

- Container orchestration platform decisions: `aws-infrastructure` or relevant platform skill.
- IaC and provisioning: `terraform-review`.
- Long-running runtime concerns: `long-running-services-architecture`.
- Security: `security-review`.

---

## Purpose

Review Docker artifacts — Dockerfiles, Compose files, image strategy, and container runtime configuration — for correctness, security, reproducibility, and production readiness.

## Responsibilities

- Evaluate Dockerfile quality and safety for development, test, and production targets
- Assess Docker Compose service definitions for correctness, networking, and reliability
- Review image tagging strategy for mutability risk and traceability
- Detect secret leakage through image layers, environment variables, or build args
- Identify EOL base images and deprecated runtime versions
- Evaluate volume patterns: bind mounts vs named volumes and their implications
- Assess container startup reliability: dependency ordering, health checks, restart policies
- Review npm/Node.js packaging patterns inside containers

## Instructions

- Review each Dockerfile target (dev, test, production) separately — they have different requirements
- Check the base image: version pinned? EOL? Alpine vs full? Security patch cadence?
- For production images: check for multi-stage build, devDependencies exclusion, minimal final layer
- For development images: check for reproducibility across developer machines, SSH agent assumptions, volume mount side effects
- For Compose files: check networking (localhost vs service name), env_file vs environment override order, restart policies, secret mounting
- For image tagging: distinguish mutable tags (`{env}:{env}`) from immutable SHA-based tags — flag mutable tags in any promotion context
- For npm: check `npm install` vs `npm ci`; check whether `--production` or `NODE_ENV=production` is set in production builds

## Heuristics

### Dockerfile — production targets
Treat as a concern when:
- Base image is EOL (Node 14, Node 16, Debian Buster, etc.)
- Single-stage: development dependencies end up in the production image
- `COPY . .` without a well-maintained `.dockerignore`: test files, Terraform, scripts, `.env` files may be included
- `npm install` instead of `npm ci`: non-deterministic builds from lock file drift
- No `--production` flag or `NODE_ENV=production`: devDependencies included in runtime
- Secrets or tokens present as `ENV` or `ARG` in intermediate layers (they persist in layer history)

### Dockerfile — development targets
Treat as a concern when:
- External tools installed without version pinning (e.g. `pip3 install awscli` without version)
- SSH or cloud credentials assumed to be available without explicit fallback or documentation
- Profile names hardcoded (e.g. `--profile aws`) that will differ between developers

### Docker Compose
Treat as a concern when:
- An environment variable used in a volume or port mapping has no fallback (e.g. `${SSH_AUTH_SOCK}` without `:-/dev/null`) — Compose fails to parse the file if the variable is unset
- `restart: "no"` vs `restart: on-failure:N` — "no" is acceptable in dev where you want to see failures; document intent explicitly
- `localhost` hardcoded in service URLs that run inside the container network — the correct hostname is the service name
- `env_file` combined with `environment:` overrides without clear documentation — effective config is split across two places
- AWS profile or credential path assumed without a documented prerequisite
- `image: someimage:latest` in any long-lived environment — mutable tag, breaks reproducibility

### Image tagging and promotion
Treat as a concern when:
- Images are tagged with environment names only (e.g. `staging-api:staging`) — these are overwritten on every build
- The same image is rebuilt (not promoted) when moving from staging to preprod/prod — "build once, promote many" requires SHA tagging
- No registry policy prevents deletion of tags that are currently deployed

## Rules

- Flag EOL base images always — they are a security risk regardless of other quality
- Distinguish "works locally" from "works across the team" — SSH agent requirements, AWS profile assumptions, and bind-mount side effects are team-wide problems
- Do not reject a Dockerfile just because it is not multi-stage if the context is dev-only — apply production standards only to production targets
- Flag mutable image tags in any context where promotion or rollback is expected to be reliable
- When a `docker-compose.yaml` serves both local dev and CI, verify that the behavior is consistent in both contexts — networking and service names may differ

## Common patterns for Node.js services

**Production Dockerfile (reference pattern):**
```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev

FROM node:20-alpine AS runtime
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
EXPOSE 3333
CMD ["node", "server.js"]
```

Key points: non-EOL base, multi-stage, `npm ci`, devDependencies omitted, minimal final layer.

**Development pattern (named volume for node_modules):**
```yaml
volumes:
  - ./:/usr/src/app
  - api_node_modules:/usr/src/app/node_modules  # prevents host OS binary conflicts
```

**SSH agent with fallback:**
```yaml
volumes:
  - ${SSH_AUTH_SOCK:-/dev/null}:${SSH_AUTH_SOCK:-/dev/null}
```

## Activity Traceability

🔧 Loading skill: `docker-review`
