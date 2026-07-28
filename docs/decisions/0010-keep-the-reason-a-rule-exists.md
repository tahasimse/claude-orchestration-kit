# 0010 — Keep the reason a rule exists: gotchas stay live, lessons are append-only

- **Status:** accepted
- **Date:** 2026-07-29

## Context
The kit had a place for decisions (`decisions/`, with Context and Alternatives — the reasoning is
the point) and a place for delivered work (`CHANGELOG.md`). It had no place for the third thing a
project produces: **rules born from failures.**

Those rules were being written down — the init gate, "the reviewer must execute", per-artifact
delegation, risk-scaled review cadence — but only as rules. Their origin, the first autopilot run
on the calc evaluator, survived nowhere but the commit message of `86a01ce`. Seven findings, one
commit message, invisible to anyone reading the repo.

That costs two things:

- **A rule without a visible reason can only be obeyed literally.** An agent that knows *why* "the
  reviewer must execute" exists — the run's only real find came from running the program, not
  reading the diff — can tell whether a new situation is covered. An agent that only has the rule
  can apply it mechanically or ignore it, and cannot generalise correctly.
- **It gets quietly undone.** Whoever tidies the PLAYBOOK next sees a line with no justification
  and removes it, and the failure that produced it happens again.

There is a second, narrower problem. `CLAUDE.md`'s gotchas had no admission test, and `CLAUDE.md`
loads into every context — the Orchestrator's and every subagent's. Anything that lands there is
paid for on every turn, forever. Without a bar, gotchas is where every stray observation
accumulates.

## Decision
Apply the split the kit already uses for `STATE.md` versus `CHANGELOG.md` (`0005`) one level down:

| | Lives in | Lifetime |
|---|---|---|
| What an agent must know now | `CLAUDE.md` → gotchas, or an agent file, or the PLAYBOOK | live, small, pruned when the problem goes away |
| Why it ever had to be known | `docs/LESSONS.md` | append-only, never pruned |

**Gotchas admission test:** without this, would an agent get its first move wrong — whatever its
role? Both halves must hold, or it routes elsewhere: role-specific to that agent's file, protocol
to the PLAYBOOK, settled choices to an ADR. Written imperative and testable, capped at about five.
The cap polices the routing, not the list.

**What counts as a lesson:** something went wrong (or the human corrected the agent) **and**
something changed because of it. No change, no lesson.

**Read policy:** not at session start — grep it, like `CHANGELOG.md`. One exception: **before
changing a rule**, read it. That is the whole mechanism for stopping a rule from being undone by
someone who never saw its reason.

**Retirement:** when a footgun is fixed, delete the gotcha and mark the lesson `retired`. The rule
was temporary; the reason is kept.

Entries are identified by date and their one-line statement. No IDs.

## Consequences
- One more file in `docs/`. Against a kit whose selling point is thinness this is a real cost,
  paid because the file is never read at session start and never hand-maintained — the same terms
  on which `CHANGELOG.md` earns its place.
- `CLAUDE.md` gains a cap it did not have, which is a tightening, not an addition.
- The read-before-changing-a-rule trigger is the only session-time cost, and it fires rarely.
- Lessons accumulate forever. At a few dozen entries this is fine; if it ever stops being greppable
  the fix is the same as for `CHANGELOG.md` — split by year, not prune.
- The kit now records reasoning for both kinds of "why": chosen (`decisions/`) and learned
  (`LESSONS.md`).

## Alternatives considered
- **Record lessons in `CHANGELOG.md`** — rejected: it answers a different question. The CHANGELOG
  says what was delivered and would bury a lesson inside one task's entry, where nobody looking for
  "why is this rule here" would find it. The lesson also outlives the task.
- **Keep only the gotchas, drop the reasons** — the status quo. Rejected for the two costs above;
  `86a01ce` is the worked example of what it loses.
- **Put lessons in `CLAUDE.md` next to the rule they explain** — rejected: `CLAUDE.md` is loaded
  into every context, so this pays for the whole history of reasoning on every turn, which is
  exactly the token leak `0005` exists to prevent.
- **A `RUNLOG.md` with per-task measurements** (review rounds, retries, token cost), queued in
  `STATE.md` as item #2 — **dropped, not deferred.** Its stated purpose was to put numbers behind
  the README's efficiency claims, but those claims are comparative ("keeps token cost low", "cold
  starts: none") and a log of kit-only runs cannot support them: that needs the same task run both
  ways, which is an experiment, not a log. The copyright holder's call was that the kit does not
  have to prove itself, so the instrument that only existed to produce that proof goes too. The
  README's comparison table stands as stated design intent, and `STATE.md` records under
  `no-numbers` that it is unmeasured by choice.
- **IDs on lessons (`L-003`)** — rejected: a join key earns its keep when entries are cross
  referenced from several places. These are referenced by date, in one file.
