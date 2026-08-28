# Velocity

**One prompt in, mergeable PRs out.**

`/full-cycle` runs a feature or bug through a five-phase pipeline: plan → tickets → parallel implementation → verification → PRs looped to green. You get grilled once, approve the plan once, and the rest runs autonomously — with your issue tracker kept current in real time and every "done" backed by evidence. Merging stays yours.

## Install

```
/plugin marketplace add shilomagen/skills
/plugin install velocity@shilo-skills
```

## Use

```
/full-cycle Our hook agent reports metrics to an endpoint behind VPN, but devs
aren't always connected — metrics get rejected and lost. Make it resilient:
buffer offline, upload when back online. Tracker team: agentside.
```

That's the whole interaction, apart from answering the grill and approving the plan.

## The principles behind it

This pipeline is a working style compressed into a skill. Eight decisions, each doing real work:

1. **The strongest model plans.** Planning, reviewing, implementing, and verifying reward intelligence; only ticket clerking goes to a cheaper model.
2. **Get grilled before anything is built.** The agent digs facts out of the code itself and interviews you only on genuine decisions — one theme per round, until a round surfaces nothing new.
3. **A fresh agent reviews the plan.** The model that wrote a plan is the worst judge of it. An adversarial reviewer with no authorship bias attacks assumptions, failure modes, blast radius, and rollback — and must report what it probed even when nothing is blocking.
4. **The verification plan is written before the code.** While design intent is sharpest, every changed behavior gets a proof procedure and a named evidence artifact. Verification later is execution, not improvisation.
5. **Tickets are cut for parallelism.** A project under your team, vertical-slice tickets with blocking links, each passing the fresh-agent test: an agent with only the ticket text and the repo can finish the work.
6. **Implementation is a dispatch loop.** The main session holds the ticket graph and keeps agents busy — parallel where files are disjoint, serialized where they overlap. Statuses move when work moves; decisions land as ticket comments, not in anyone's memory.
7. **Verification is evidence-based and environment-aware.** Client changes: screenshots of every state, pixel-perfect Figma comparison, re-render profile, bundle delta. Backend changes: real requests, actual DB rows, EXPLAIN plans, dependency-failure drills — with temporary simulation endpoints when a flow can't be reached from outside (removed before ship). "Works correctly" with nothing attached goes back to the agent.
8. **PRs are cut for the reviewer, then looped to green.** One PR if it reads in a sitting; stacked PRs along ticket seams if not. Each loops — CI logs read, review-bot comments addressed, stack rebased — until green, approved, and thread-clean. The loop has no early exit.

## Per-repo configuration

Drop a `.fullcycle.md` at your repo root:

```markdown
tracker: linear, team agentside
review bot: CodeRabbit

## verify: client
Run against localhost:3000; design system in docs/DESIGN_SYSTEM.md — no blue, ever.

## verify: backend
Stack runs via `make dev`. ClickHouse and Postgres creds in README.
```

Anything missing gets asked once during the grill, and the skill offers to save the answers here so it never asks again.

Without Linear (or any tracker MCP), tickets land as files under `.fullcycle/tickets/` and the pipeline works the same. Without a review bot, CI green is the bar.

## Shape of the skill

```
full-cycle/
├── SKILL.md            # the orchestrator: pipeline map, models, the one human gate
├── phases/             # read one at a time, only when entered
│   ├── plan.md         #   investigate → draft → grill → adversarial review → verification plan → gate
│   ├── ticketize.md    #   project + vertical-slice tickets + blocking links
│   ├── implement.md    #   the dispatch loop + per-ticket contract
│   ├── verify.md       #   evidence discipline + failure routing
│   └── ship.md         #   PR cutting + the green loop
└── references/         # charters handed to fresh subagents
    ├── plan-review.md
    ├── verify-client.md
    └── verify-backend.md
```

Phases are read progressively on purpose: an agent that can see the finish line rushes the step in front of it. Each phase ends on an explicit completion criterion, and the next file opens only when it's met.
