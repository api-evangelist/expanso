---
name: Submit and monitor a distributed compute job
description: Submit a job to the Bacalhau orchestrator, follow its state history, stream logs, and fetch results.
api: openapi/bacalhau-openapi-original.json
operations:
  - orchestrator/putJob
  - orchestrator/getJob
  - orchestrator/listHistory
  - orchestrator/logs
  - orchestrator/jobResults
---

# Submit and monitor a distributed compute job

Use the Bacalhau Orchestrator API (base path `/api/v1`) to run a job close to
its data and follow it to completion.

## Steps

1. **Submit the job** — `PUT /api/v1/orchestrator/jobs` (`orchestrator/putJob`)
   with a `Job` spec in the request body. Set `idempotencyToken` so a retried
   submission is not executed twice (see `conventions/bacalhau-conventions.yml`).
   The response returns `JobID` and `EvaluationID`.
2. **Check job state** — `GET /api/v1/orchestrator/jobs/{id}` (`orchestrator/getJob`)
   using the `JobID` from step 1.
3. **Follow the history** — `GET /api/v1/orchestrator/jobs/{id}/history`
   (`orchestrator/listHistory`) for the state-change timeline; it is
   cursor-paginated (`limit` + `next_token`, response `Items` + `NextToken`).
4. **Stream logs** — `GET /api/v1/orchestrator/jobs/{id}/logs`
   (`orchestrator/logs`); this upgrades the connection (HTTP 101) to stream
   live job logs.
5. **Fetch results** — once complete, `GET /api/v1/orchestrator/jobs/{id}/results`
   (`orchestrator/jobResults`) to retrieve published outputs.

## Rules

- Job/node ids are opaque and case-sensitive; use the id returned by putJob.
- On `400` validate the job spec; on `500` retry with backoff (see
  `errors/bacalhau-problem-types.yml`).
- To cancel, call `DELETE /api/v1/orchestrator/jobs/{id}` (`orchestrator/stopJob`).
