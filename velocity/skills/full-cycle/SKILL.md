---
name: full-cycle
description: Run a feature or bug from raw idea to mergeable PRs — plan, grill, adversarial review, tickets, parallel implementation, evidence-backed verification, stacked PRs looped to green.
disable-model-invocation: true
argument-hint: "<the feature or bug> [tracker team, constraints]"
---

# Full Cycle

One prompt in, mergeable PRs out. Five phases run the work from raw idea to PRs that are green, review-bot-clean, and ready for a human to merge. The human is grilled once, approves the plan once, and everything after that runs autonomously — every claim backed by evidence, every ticket kept current, every PR looped until green.

Two ideas carry the whole pipeline:

- **Fresh eyes at every boundary.** The model that writes a plan is the worst judge of it; the agent that implements a ticket is the worst verifier of it. Each phase hands its output to an agent that didn't produce it — a *fresh* subagent every time, never a fork: a fork inherits this conversation, and with it the authorship bias the boundary exists to remove.
- **Evidence or it didn't happen.** Every "done" carries an artifact — a screenshot, a query and its rows, a test log, a green check. A claim without evidence is a claim, not a completion.

## The pipeline

Work the phases in order. Read each phase file only when you enter that phase — the current phase deserves undivided attention, and the later ones will still be there.

| # | Phase | File | Runs as | Model | Gate |
|---|-------|------|---------|-------|------|
| 1 | Plan | [phases/plan.md](phases/plan.md) | main session — human in the loop | strongest | plan closed and **user approved** |
| 2 | Ticketize | [phases/ticketize.md](phases/ticketize.md) | one subagent, audited on return | fast | every ticket passes the fresh-agent test |
| 3 | Implement | [phases/implement.md](phases/implement.md) | main session dispatching per-ticket subagents | strongest | every implementation ticket Done, with evidence |
| 4 | Verify | [phases/verify.md](phases/verify.md) | main session dispatching verification subagents | strongest | every verification ticket Done, with evidence |
| 5 | Ship | [phases/ship.md](phases/ship.md) | main session | strongest | every PR green, bot-clean, thread-clean |

**Models.** "Strongest" is the model running this session, or the most capable one the Agent tool offers — whichever is stronger. "Fast" is a cheaper tier. Planning, reviewing, implementing, and verifying reward intelligence; ticket clerking doesn't — spend accordingly.

**The one human gate** sits at the end of phase 1. Everything before it is conversation; everything after it creates outward-facing artifacts — tickets, branches, PRs — so nothing passes the gate without the user's explicit approval of the plan. After the gate, run to completion without asking permission again; report progress as phases close.

## Configuration

Check the repo root for `.fullcycle.md` before starting. It carries the per-repo facts the pipeline needs:

```markdown
tracker: linear, team <team-name>       # or: local
review bot: CodeRabbit                  # absent → CI green is the bar
                                        # no CI either → the repo's own checks, run locally

## verify: client
<repo-specific instructions that extend references/verify-client.md>

## verify: backend
<how to run the stack, credentials, what to check>
```

Anything missing gets asked during the grill in phase 1 — fold the questions in there rather than asking up front. Then offer to write the answers to `.fullcycle.md` so the next run never asks.

## State across phases

Create `.fullcycle/<run-slug>/` at the repo root when the run starts. It holds the run's artifacts — `plan.md`, `verification-plan.md`, and every evidence file agents capture (screenshots, logs, query output), so evidence survives worktree cleanup and tickets can link to something durable. The directory stays out of the PRs.

Phase 2 moves the plans' content into the tracker, and from then on **the tracker is the single source of truth**: statuses, decisions, and evidence live on the tickets, and any agent can be pointed at a ticket and trust it. Use the team's own workflow states — "Done" means whatever the team's terminal state is called. Keep it current in real time — status moves when work moves, comments land when decisions land — not reconstructed at the end.

Subagents don't share this conversation: the dispatch prompt, the files it names by absolute path (this skill's directory, the run directory, the repo root), and the repo's own agent docs are everything they know. The repo's rules bind them alongside anything you dispatch — the dispatch adds to those rules, never replaces them.
