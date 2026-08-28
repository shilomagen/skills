# Phase 4 — Verify

Implementation evidence proved each ticket did what it said. This phase proves the *feature* does what the plan promised — in a running system, by an agent that didn't build it. Dispatch one strongest-model subagent per verification ticket.

## Dispatch

Each verification ticket names its environment. The subagent gets the ticket (proof procedure and required evidence) plus the matching baseline:

- client → `<skill-dir>/references/verify-client.md`
- backend → `<skill-dir>/references/verify-backend.md`
- both → both files
- anything else — a CLI, a daemon, a library — take verify-backend.md as the nearer baseline and adapt

plus the repo's own `verify:` notes from `.fullcycle.md`, which extend — and on conflict, override — the baselines.

Verification agents that share one running stack run in sequence, against a stack brought up once from the integration branch — parallel agents fighting over the same containers and data produce evidence of each other, not of the feature. Evidence files land in the run directory so tickets can link to them.

## Evidence discipline

Verified means the artifact exists: the screenshot taken, the query and its rows, the request and its response, the test log. A verification report that says "works correctly" with nothing attached goes back to the agent. Reading source is how implementation gets reviewed; behavior is verified by running the system and capturing what it did.

## When verification fails

A failure here is the pipeline working. File a bug ticket in the project — symptoms, the failure evidence, the verification ticket it came from — blocked into the graph, and route it through the phase 3 dispatch loop. Re-verify after the fix. The phase ends by making the system pass the bar, never by lowering it.

**Phase complete when:** every verification ticket is Done with its evidence attached, and every failure it surfaced has been fixed and re-verified.
