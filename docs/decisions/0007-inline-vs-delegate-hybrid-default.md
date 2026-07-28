# 0007 — Route inline vs delegate by context cost; `full-delegate` lives on its own branch

- **Status:** accepted
- **Date:** 2026-06-25 (recorded retroactively 2026-07-29)

## Context
Once the Orchestrator can spawn subagents, "when should it?" becomes the live question. Delegating
everything is attractively simple and keeps the supervisor's context minimal, but a cold subagent
costs a spawn plus a re-read of context the Orchestrator already has loaded — which is absurd
overhead for a one-line edit. Doing everything inline is equally simple and fails the other way:
exploratory work ("find where this bug comes from") drags tens of thousands of tokens of file
reading into the supervisor's context, where it is then re-sent on every subsequent turn.

The variable that actually separates the two cases is not size and not role. It is whether the
*reading* the work requires is disposable.

## Decision
Route by context cost, with one litmus test: **is the reading disposable?** Work the Orchestrator
can finish in a couple of lines with what it already has loaded — a rename, a one-line edit, a doc
tweak — is done **inline**. Anything exploratory or context-heavy — tracing a bug, searching the
repo, a multi-file feature, and every review or verification — is **delegated**, so the disposable
wading happens in a throwaway context and comes back as a compact result. This hybrid is the
default and lives on `main`.

The pure extreme is kept, not discarded: the **`full-delegate`** branch delegates everything,
including the 100-token job. It pays off only when the project is large enough that minimizing the
supervisor's context is worth spawn overhead on every task.

## Consequences
- The supervisor's context stays small where it matters without paying spawn cost where it does not.
- The rule needs judgment at the margin. "Disposable reading" is a good test, not a mechanical one,
  and the Orchestrator will sometimes call it wrong — cheaply in both directions.
- Two branches to maintain. `full-delegate` will drift from `main` unless it is deliberately
  rebased, and it currently has (tracked as `branch-drift` in `docs/STATE.md`).
- Reviews and verification are delegated unconditionally, regardless of size — they need fresh eyes
  and execution, not just context economy.

## Alternatives considered
- **Delegate everything, always** — kept, but as `full-delegate` rather than the default: paying a
  cold start plus re-read to change one line is worse than doing it, unless the project is large
  enough to invert that.
- **Inline everything; use subagents only for review** — rejected: it makes the Orchestrator's
  context the dumping ground for exploration, which is the specific leak this kit exists to close.
- **Route by task size (lines changed, files touched)** — rejected: size correlates weakly with
  context cost. A one-line fix can require reading twenty files to locate.
