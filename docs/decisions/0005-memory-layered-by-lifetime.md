# 0005 — Project memory is layered by lifetime, not by topic

- **Status:** accepted
- **Date:** 2026-06-23 (recorded retroactively 2026-07-29)

## Context
The default way people give a coding agent project memory is one status document that grows
forever and gets re-read at the start of every session. It works for a week. After that, every
session pays a token tax on dead history to find three live facts, and because the file is
expensive to read it gets read badly — skimmed, or worse, summarized by the agent into something
subtly wrong.

Splitting it by topic (architecture doc, progress doc, notes doc) does not fix this, because the
split does not follow how often each fact changes or who needs it.

## Decision
Split project memory by **lifetime and read frequency**:

| File | Lifetime | Read when |
|------|----------|-----------|
| `docs/STATE.md` | changes constantly, stays small | every session, first |
| `docs/decisions/` | appended rarely, frozen forever | every session (cheap) |
| `docs/CHANGELOG.md` | append-only, grows forever | only to look up a past fact — grep, never read in full |
| `docs/PLAYBOOK.md` | rarely changes | when orchestrating |

`STATE.md` is capped by convention at roughly one screen. If it outgrows that, the overflow is
history or rationale in the wrong file and belongs in `CHANGELOG.md` or `decisions/`. Underneath
sits the rule that keeps all of it honest: **code = truth, docs = index** — when a doc and the code
disagree, the code wins and the doc gets fixed.

## Consequences
- Always-read cost stays flat as the project grows, which is the point.
- History stays available without being paid for, at the cost of being greppable rather than
  narrative. Finding "when did we switch X?" needs a search, not a scroll.
- It only works if the discipline holds. A STATE file that silently accretes history is the same
  ever-growing status doc with a smaller name.
- Frozen decisions get their own always-read tier, which is what stops the same architecture choice
  being re-argued every few sessions.

## Alternatives considered
- **One `PROJECT.md` with everything** — rejected: the token tax this design exists to remove.
- **Split by topic (architecture / progress / notes)** — rejected: the boundaries do not match
  read frequency, so every session still reads slow-changing bulk to find fast-changing facts.
- **No file memory; rely on the agent's context and git history** — rejected: subagents start cold,
  and `git log` records what changed, never why it was decided.
