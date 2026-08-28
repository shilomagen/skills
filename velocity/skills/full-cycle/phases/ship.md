# Phase 5 — Ship

Turn the integration branch into PRs a human can actually review, then loop each one to green. Reviewability decides the shape; the loop decides the finish.

## Cut the PRs

Before cutting anything, run the repo's own post-change hygiene — docs that promise behavior the change altered, generated files, whatever the repo's rules demand. Repo rules outrank this file.

- One concern, reviewable in one sitting (~400 changed lines is the ceiling) → **one PR**.
- Bigger → **stacked PRs**, cut along ticket boundaries — the per-ticket commits from phase 3 are the seams. The bottom PR targets the default branch; each PR above targets the one below it, and when a lower PR merges, retarget the next one down to the default branch.
- Every PR description carries: what and why in two paragraphs, the tickets it closes, its position in the stack ("2/4, based on #123"), and the evidence highlights from verification.

## The green loop

Run it per PR, bottom of the stack first — a fix low in the stack rebases everything above it, so bottom-up touches each PR once:

1. Push, then watch checks (`gh pr checks --watch`).
2. CI failure → read the failing log, not just the status; fix the cause; push. A flaky-looking failure gets one re-run; a second failure is real.
3. Review-bot comments (CodeRabbit, or whatever the repo uses) → address every one: a fixing commit, or a reply giving the concrete reason it stands. Resolve the thread either way.
4. After any change to a lower PR, rebase the stack above it and re-loop the affected PRs.
5. A PR exits the loop when CI is green (no CI configured → the repo's own checks run locally, and say so in the PR), the bot is satisfied — an approval where it issues one, zero unaddressed comments where it doesn't — and no thread is unresolved.

Fixes inside the loop are made directly; smallness is the guard. A fix that grows into real work goes back to a ticket and through the phase 3 loop instead. The loop itself has no early exit: "CI is probably fine" and "the comment is minor" both mean the loop is still running.

## Close out

Merging is the human's move — the pipeline delivers PRs that are ready for it.

- Link each PR to its tickets; move the project to Done.
- Report to the user: PR URLs in stack order with one line each, the project URL, and what a human reviewer should look at first.

**Phase complete when:** every PR is green, bot-clean, and thread-clean; the project is Done; the report is delivered.
