# Phase 3 — Implement

The main session becomes a dispatcher: it holds the ticket graph, keeps agents busy, and verifies their work — it writes no code itself. All code is written by per-ticket subagents on the strongest model.

## The dispatch loop

Repeat until no implementation ticket remains open:

1. **Ready set** — the tickets whose blockers are all Done.
2. **Partition** — ready tickets that touch disjoint files run in parallel, one subagent each, with worktree isolation keeping them off each other's changes. Worktrees isolate *files only*: agents that share runtime — a dev stack, ports, containers, a database, on-disk state — still collide, so partition on shared runtime too. Tickets that overlap on either run in sequence. When in doubt, serialize: a collision costs more than the parallelism saves.
3. **Dispatch** — each subagent gets the per-ticket contract below, the ticket ID and full text, and the branch to base on (its worktree creates the ticket branch *from* it — the integration branch itself stays checked out only in the main repo; git refuses one branch in two worktrees).
4. **Verify before Done** — when a subagent reports back, check its evidence against the ticket's acceptance criteria yourself. Criteria met, evidence attached → mark the ticket Done. Anything short → back to a fresh subagent with exactly what's missing. The implementer's word is not the bar; the evidence is.

## Per-ticket contract (goes into every dispatch)

- Move the ticket to In Progress with a comment: branch name, approach in one line.
- One branch per ticket; commits reference the ticket ID. Commit as the work lands — uncommitted worktree changes die with the agent, and a replacement agent picks up from the branch.
- Tests are part of the work, not a follow-up: new logic lands with the test that pins it.
- Run the repo's own checks — typecheck, lint, tests, found in the repo's scripts — and fix what they catch.
- Close with a ticket comment: what changed (files, decisions taken) and the evidence — test output, the behavior demonstrated.

## Branch discipline

Start from a clean working tree — the gate in phase 1 already surfaced anything dirty. Cut one integration branch for the effort from the repo's default branch, named after the project. Ticket branches build on it (or on the ticket branch they're blocked by); merge each finished ticket branch back into it in dependency order. The per-ticket commits are the seams phase 5 cuts stacked PRs along — keep them clean and attributed.

**Phase complete when:** every implementation ticket is Done with its evidence comment, and the integration branch carries all of it with the repo's checks green.
