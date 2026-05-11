# Integration Context

External services, APIs, and dependencies that the platform integrates with.

Other services are integration context, not architectural references. Do not copy their patterns into new code.

---

## AIS Providers (Banking Aggregation)

| Service | Repo | Notes |
|---|---|---|
| AfterBanks | `afterbanks`, `afterbanks-client` | AIS provider — account information and payment initiation |
| Nordigen | `nordigen-ais`, `nordigen-client-nodejs` | AIS provider — alternative to AfterBanks |
| Qonto | `qonto-client` | Business banking integration |

## Cloud Infrastructure

| Service | Usage |
|---|---|
| AWS Lambda | Serverless compute — event handlers, scheduled jobs, ETL |
| AWS S3 | Document storage |
| AWS DynamoDB | NoSQL storage for specific workloads |
| AWS Glue | ETL jobs for data pipelines |
| AWS Step Functions | Orchestration — NOA lifecycle (`noa-bpm`) |

## Data Platform

| Service | Repo | Notes |
|---|---|---|
| Databricks | `data-infrastructure`, `data-pipelines` | Data team — pipelines, models, ETL |

## Developer Tooling

| Service | Usage |
|---|---|
| Atlassian (Jira / Confluence / Bitbucket) | Project management, documentation, code hosting |
| Rollbar | Error tracking across services |