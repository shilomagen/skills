# Phase 2 — Ticketize

Dispatch one fast-model subagent whose prompt names this file, `plan.md`, and `verification-plan.md` by absolute path, plus the tracker configuration. Tickets are the pipeline's memory from here on: phase 3 agents see a ticket and the repo, nothing else — the quality of this phase decides how independent they can be.

## The fresh-agent test

Every ticket must pass it: **a fresh agent given only the ticket text and the repo can finish the work.** No "as discussed", no pointer back to the planning conversation — the context lives in the ticket:

- what to build and why — the plan's relevant slice, one paragraph
- the files involved, carried over from the plan's investigation
- acceptance criteria that are checkable, not aspirational
- the evidence to attach when done

## Structure

1. Create the project under the configured team; the project description carries the plan.
2. Cut implementation tickets as vertical slices — each proves one thread of the design end to end — rather than horizontal layers that only work once everything is done. Slices are what makes parallel work possible.
3. Declare dependencies as blocking links. A ticket is **ready** when its blockers are Done; the dependency graph is what phase 3 parallelizes against, so a false edge costs wall-clock and a missing edge costs a merge conflict.
4. The verification plan becomes verification tickets, each blocked by the implementation tickets it verifies. Copy the proof procedure and required evidence into the ticket.

With a local tracker, the same structure lands as files under `.fullcycle/tickets/` — one file per ticket, frontmatter carrying status and blockers.

## Audit on return

The subagent certifying its own tickets proves nothing, so the main session audits when the report comes back: read every ticket, and hold the weakest-looking one hard against the fresh-agent test — could an agent with only this text and the repo actually finish? Gaps go back to the subagent named precisely; the phase gate is the audit passing, not the subagent reporting.

**Phase complete when:** every item of the plan maps to a ticket, the audit confirms every ticket passes the fresh-agent test, the dependency graph has no cycles, and the project URL and ticket list are in hand.
