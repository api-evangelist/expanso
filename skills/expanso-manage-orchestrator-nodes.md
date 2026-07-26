---
name: Inspect and manage orchestrator nodes
description: List and inspect Bacalhau orchestrator/compute nodes and update node membership.
api: openapi/bacalhau-openapi-original.json
operations:
  - orchestrator/listNodes
  - orchestrator/getNode
  - orchestrator/updateNode
  - agent/version
---

# Inspect and manage orchestrator nodes

Use the Bacalhau Orchestrator API (base path `/api/v1`) to view the compute
network and manage node membership.

## Steps

1. **Confirm the server version** — `GET /api/v1/agent/version`
   (`agent/version`) to verify the orchestrator you are talking to.
2. **List nodes** — `GET /api/v1/orchestrator/nodes` (`orchestrator/listNodes`);
   cursor-paginated (`limit` + `next_token`, response `Items` + `NextToken`).
3. **Inspect a node** — `GET /api/v1/orchestrator/nodes/{id}`
   (`orchestrator/getNode`). A `404` means the node id does not exist.
4. **Update a node** — `PUT /api/v1/orchestrator/nodes/{id}`
   (`orchestrator/updateNode`) with a `PutNodeRequest` body to change
   membership/approval state.

## Rules

- Node membership changes are control-plane actions; confirm the node id from
  listNodes before updating (see `agentic-access/expanso-agentic-access.yml`).
- Handle `400` (bad request), `404` (unknown node), and `500` per
  `errors/bacalhau-problem-types.yml`.
