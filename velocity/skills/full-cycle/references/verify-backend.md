# Backend verification baseline

Verify against a running instance — the repo's dev stack, not a mental model of it. Every claim carries its artifact: the request and its response, the query and its rows, the log line, the metric.

## Endpoints

- Exercise every touched endpoint with real requests: the happy path, then the unhappy ones — invalid input, missing auth, wrong tenant, oversized payload.
- Contract: response shapes match what the plan and any schema or types promise, error shapes included.

## Data

- After each mutating operation, inspect the actual rows in the actual store — values, not just counts — and confirm the read path returns what was written.
- New queries: EXPLAIN them. Confirm they hit indexes, and that aggregation happens in the database rather than in application code looping over rows.

## Hard-to-reach flows

When a flow can't be triggered from outside — a queue consumer, a retry path, a scheduled job — add a temporary simulation endpoint or script that drives it deterministically. Simulation code is scaffolding: remove it once the evidence is captured, and verify the removal landed before ship.

## Resilience

When the change involves a dependency — a queue, a cache, an external API — verify the failure half: take the dependency down mid-flow, capture the behavior the plan promised (buffering, retry, graceful degradation), then bring it back and confirm recovery drains cleanly.

## Operational

- The service starts clean: no new errors or warnings in startup logs.
- The change's hot path holds at a realistic call rate — a quick load sanity check, not a benchmark suite.
