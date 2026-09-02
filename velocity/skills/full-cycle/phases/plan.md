# Phase 1 — Plan

Runs in the main session: this is the only phase where the human is present, so everything that needs their judgment happens here. The phase closes a plan; it writes no code.

## 1. Investigate

Ground the design in the actual code before designing anything. Read the code paths the ask names — the entry points, the data flow, the exact place the problem lives — and inventory what already exists that the change should extend rather than duplicate. The investigation is done when you can name the specific files and mechanisms the change will touch.

A design written before this step reads plausible and lands wrong.

If the investigation shows the ask is already built, or dissolves the problem into a different one, stop and re-scope with the user before drafting anything — the pipeline exists to ship what's needed, and continuing past this finding ships a duplicate.

## 2. Draft the design

Write `plan.md` in the run's scratch directory:

- **Problem** — what breaks or is missing today, with the evidence from the investigation
- **Approach** — the chosen design, concrete to file level
- **Alternatives** — what else was considered and why not
- **Blast radius** — everything the change can affect: callers, data, deploys, other consumers
- **Open questions** — everything you can't settle alone; these feed the grill

## 3. Grill the user

**One round.** Every question goes out in a single batch, the answers come back, and the grill is over — a second round is the tedium this rule exists to prevent. The user can reopen it; you can't.

- Facts are your job — dig them out of the code *before* you compose the round. A question waiting on a lookup is a second round in disguise.
- Decisions are the user's: scope, failure modes, edge cases, rollout, non-goals, and the configuration gaps (tracker, team, review bot) `.fullcycle.md` didn't fill. Number each question and give it a recommended answer.
- **7 questions** is the ceiling — the ones that actually change the plan. Anything below the cut, or that only makes sense once another question is answered, takes your recommendation instead of a question.
- If a grilling skill is installed, invoke it on the draft — one round, and its own stopping rule doesn't override that.

Record the answers in `plan.md`; decisions get recorded, not remembered. Anything the user didn't settle stands as your recommendation, listed at the top of the plan so they read it first at the gate.

## 4. Adversarial review

Dispatch a fresh strongest-model subagent:

> Read `<skill-dir>/references/plan-review.md` and follow it. The plan is at `<scratch>/plan.md`. The repo root is `<path>`.

You wrote this plan, so you're the wrong judge of it — that's the reviewer's whole job. For every finding that comes back: change the plan, or record in `plan.md` why it stands. Findings only the user can arbitrate go to the user. If the findings forced significant changes, dispatch a fresh reviewer at the new version; the review is closed when a fresh reviewer returns no blocking findings.

## 5. Verification plan

For every behavior the plan changes, write how it will be proven, in `verification-plan.md`:

- the behavior, and its environment: client, backend, or both
- the proof procedure, and the **evidence artifact** it must produce — a screenshot, a query and its rows, a request and its response, a test log

Write it now, while the design intent is sharpest. The agent verifying in phase 4 sees only what's written here.

## 6. The gate

Present the plan and the verification plan to the user for approval. This is the pipeline's only gate: after a yes, tickets, branches, and PRs get created without further permission. On edits, fold them in and present again.

Two practicalities to surface alongside the plan, so the autonomous run actually runs autonomously:

- the working tree must be clean before phase 3 cuts branches — anything dirty is the user's to resolve now
- the run will edit files, run commands, and call the tracker continuously; suggest a permission mode that allows that (or the user should expect prompts)

**Phase complete when:** the review is closed, the verification plan covers every changed behavior, and the user has approved.
