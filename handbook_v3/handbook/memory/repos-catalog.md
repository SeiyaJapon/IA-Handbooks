# Repos Catalog

Index of repositories in the Kintai organization (`bitbucket.org/kintai-digital`), grouped by domain.

Last updated: 2026-04-29.

---

## Cross (all profiles)

| Repo | Description |
|---|---|
| `ai-config` | AI configuration bundle — agents, skills, commands, rules for Claude Code and Cursor |
| `generators` | Project generators for lambdas, libraries, Databricks apps, Angular dashboards |
| `meta-all` | Meta-repository — profile setup and repo catalog |

---

## Platform Engineering

Core production stack: API, dashboard, lambdas, infrastructure, internal libraries.

| Repo | Description |
|---|---|
| `api` | Operational API |
| `dashboard` | Backoffice frontend (Angular) |
| `events` | Event management, tracking |
| `types-kintai` | Shared TS types, interfaces |
| `ts-tools` | TS utilities — parsers, validators, formatters |
| `node-tools` | Node utilities — fs helpers, env, etc. |
| `notifications` | Centralized communications — email, burofax, etc. |
| `bank-aggregation` | Banking aggregation (provider integrations) |
| `reports` | Periodic report generation |
| `data-gathering` | Data extraction from official documents (SS, invoices) |
| `documents` | Document management — storage, metadata, lifecycle |
| `afterbanks` | AIS provider — AfterBanks integration |
| `afterbanks-client` | Node client for AfterBanks |
| `nordigen-ais` | AIS provider — Nordigen integration |
| `nordigen-client-nodejs` | Node client for Nordigen |
| `qonto-client` | Qonto integration client |
| `rule-engine-handler` | Rule engine handler (automated decisions) |
| `aws-node-client` | Kintai AWS client (SDK wrappers) |
| `common-lambdas` | Shared generic lambdas / processes |
| `bulk-handler` | Bulk process handler |
| `wallets` | Wallet management — integrations, webhooks, async processes |
| `watchmen` | Watchdogs / health checks |
| `terraform-modules` | Reusable Terraform modules |
| `infrastructure` | Shared hard infrastructure — main DB, ECS clusters, network (VPC, subnets), IAM |
| `noa-bpm` | Step Function for NOA lifecycle management |
| `api-client-nodejs` | TypeScript client for the Kintai API |
| `dev-env` | Development environment / dev tooling |
| `ais` | General AIS integration (cross-provider wrappers) |

---

## Data Engineering

Pipelines, Databricks infrastructure, ETL.

| Repo | Description |
|---|---|
| `data-pipelines` | Data pipelines and ETL |
| `data-infrastructure` | Data infrastructure (Databricks workspace, jobs, clusters) |
| `ais-categorization-model` | AIS categorization model (transactional) |
| `kintai-ais-categorization-model` | Kintai-specific AIS categorization model |
| `glue-libs` | Shared libraries for AWS Glue |
| `etl-python-tools` | Shared Python tools for ETL |
| `ocr-invoices` | OCR on invoices for structured extraction |
| `python-tools` | Python utilities — parsers, helpers |

---

## Data Builders

Data products — agents, human-review tools, simulators.

| Repo | Description |
|---|---|
| `daily-ledger-agent` | Agent for the daily ledger |
| `daily-ledger-management` | Daily ledger management / operations |
| `invoice-reconciliation` | Human review for invoice reconciliation |
| `receivables-simulator` | Receivables simulator |
| `receivables-calculator` | Receivables calculator |
| `categorization-general-journal` | General accounting journal categorization |

---

## AI / Reconciliation

AI agents and platform.

| Repo | Description |
|---|---|
| `ai-platform` | Common AI platform (infrastructure for Kintai agents) |
| `reconciliation-api` | Reconciliation API — matching, feedback |
| `reconciliation-web` | Human review web — matching, feedback |
| `reconciliation-engine` | Reconciliation engine |
| `reconciliation-feedback-agent` | Feedback agent for reconciliation |
| `reconciliation-omnibroker-agent` | Omnibroker agent for reconciliation |
| `reconciliation-broker-agent` | Broker agent for reconciliation |
| `risk-rag` | Risk RAG — retrieval-augmented generation on risk data |

---

## Unclassified (pending assignment)

Productive repos not yet categorized.

| Repo | Notes |
|---|---|
| `webhook-inserter` | To classify |
| `payment-dashboard` | Payment dashboard |
| `monitoring` | Monitoring (data?) |
| `risk-scoring` | Risk scoring |
| `risk-predictive-model` | Predictive risk model |
| `oauth` | Internal OAuth service |
| `contracts` | Shared contracts / events? |
| `reconciliation` | Possibly pre-dates the reconciliation-* stack |
| `data-aggregation-pipeline` | To classify |

---

## Notes

- Shared utilities live in `ts-tools`, `node-tools`, `python-tools`, `types-kintai`, `aws-node-client`.
- Before writing a new utility function, check these repos first.
- Integration with other services must be explicit and treated as external context, not architectural reference.
