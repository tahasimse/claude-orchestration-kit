# 0004 — Two human gates: plan and merge

- **Status:** accepted
- **Date:** 2026-06-23 (recorded retroactively 2026-07-29)

## Context
Where the human sits in an agent pipeline is the whole design. Approve everything and you are the
message bus again — every hand-off is a copy-paste and the agents buy you nothing. Approve nothing
and you find out what was built only after it is merged, which is where unverified code and quietly
re-litigated decisions land.

The two moments where a human's judgment is not substitutable are **before** work starts (is this
the right thing, in the right place?) and **before** it becomes shared truth (do I accept this into
the trunk?). Everything between those two — spawning the builder, running tests, routing reviewer
findings back for a fix, looping until the verdict is *ready* — is mechanics, and mechanics is what
agents are for.

## Decision
The pipeline stops at exactly two gates: **Gate 1, human approves the plan**, and **Gate 2, human
approves the merge**. Steps 2–4 of the task lifecycle (plan → build → review → fix loop) run
without asking. Ceremony scales to risk: a small obvious fix may skip the formal plan and go
build → review; an architectural task presents options at Gate 1 and records the choice as an ADR
before any code is written.

`/autopilot` is the deliberate exception: it collapses to **one** gate (plan + GO) and runs
unattended after it. It buys that back with its own rails — branch-only so nothing irreversible
happens, tests as the definition of done, anti-spin after roughly three failed attempts, and a
morning report. The trade is stated plainly in the README rather than hidden: you swap the
pre-merge human review for unattended throughput, and the branch is your morning checkpoint.

## Consequences
- The human's attention goes to the two decisions that are actually theirs, not to relaying
  messages between roles.
- A bad plan approved at Gate 1 costs a full build cycle. That is the accepted failure mode, and
  it is why the planner is a reasoning-heavy role on `opus` (see `0006`).
- The reviewer must be genuinely adversarial, because between the gates nobody else is looking.
- In `/autopilot`, the reviewer subagent plus green tests are the *only* safety net before the
  branch. That is a real reduction in assurance, taken knowingly.

## Alternatives considered
- **Approve every step** — rejected: that is the manual role-chat workflow the kit exists to kill.
- **One gate only (plan), always** — rejected as the default: fine for an overnight run on a
  branch, too loose for everyday work where merging is the point.
- **Zero gates, fully autonomous** — rejected: nothing then distinguishes "the tests pass" from
  "this is the right thing to have built."
