# LESSONS

> Why a rule exists. When something goes wrong and a rule, config or doc changes as a result,
> the change goes where agents read it — and the *reason* goes here.
>
> **A lesson is recorded only when both are true:** something went wrong (or the human corrected
> the agent), **and** something changed because of it. No change, no lesson — that is a note, and
> notes do not belong in a file that lives forever.
>
> **Not read at session start.** Append-only, like `CHANGELOG.md`; grep it when you need a fact.
> **One exception:** before changing a rule — editing the gotchas in `CLAUDE.md`, the PLAYBOOK, or
> an agent file — read this first. Otherwise you will quietly undo a rule that exists for a reason
> you never saw.
>
> Rules get pruned when the problem goes away. Entries here do not: mark them `retired` instead, so
> the trail survives the rule.
>
> Where a rule belongs, and what earns a place in gotchas: `PLAYBOOK.md` → "Gotchas and lessons".
>
> **Entries dated 2026-06-23 were backfilled on 2026-07-29** from the commit message of `86a01ce`,
> which was the only place the first autopilot run's findings had been written down. That is the
> failure this file exists to stop repeating.

<!-- New entries go directly below this line, newest on top. -->

## 2026-07-29 — A branch can carry an idea; treating every branch as work created false debt
- **Source:** the human said so, correcting the assumption behind a plan under review.
- **Result:** `STATE.md` debt `branch-drift` (rebase or retire `full-delegate`) replaced by
  `branch-labels` (declare what a branch holds and where it was cut). `decisions/0009` states the
  rule; `README.md` on `main` declares both idea branches.
- **Status:** in force

## 2026-07-29 — Self-hosting in `docs/` would have shipped this repo's own frozen decisions into copiers' projects
- **Source:** review of `decisions/0002` before it reached `main`. The install copies `docs/`
  wholesale, so a copier's `decisions/` would have arrived holding `0002`–`0009` — which
  `CLAUDE.md` tells their agent are binding.
- **Result:** `decisions/0009` supersedes `0002`: `main` stays a clean template, self-hosting lives
  on this unmerged snapshot branch.
- **Status:** in force

## 2026-06-23 — The reviewer's one real find came from running the program, not from reading the diff
- **Source:** first autopilot run, calc evaluator (`86a01ce`). Reading the diff surfaced nothing
  that mattered; executing adversarial inputs did. Recorded as the highest-value change of the run.
- **Result:** `PLAYBOOK.md` and `.claude/agents/reviewer.md` — the reviewer must **execute**, not
  only read.
- **Status:** in force

## 2026-06-23 — A fresh builder per commit drifts on tightly-coupled work
- **Source:** same run. Cold per-commit builders re-derived the design each time and diverged in
  naming and structure on work that grew rung by rung.
- **Result:** `PLAYBOOK.md` — delegate one builder per *coherent artifact*, not per commit. Split
  across builders only when the pieces are genuinely independent.
- **Status:** in force

## 2026-06-23 — Reviewing every rung separately cost far more than it caught
- **Source:** same run. A batch review over the whole diff caught what mattered on low-risk coupled
  rungs, at an estimated fifth of the per-unit cost — an impression, never instrumented.
- **Result:** `PLAYBOOK.md` — review cadence scales with risk, like ceremony does.
- **Status:** in force

## 2026-06-23 — A placeholder `STATE.md` was read past as noise instead of stopping the session
- **Source:** same run. The agent treated an uninitialized STATE as an empty one and started work
  without orienting.
- **Result:** `CLAUDE.md` read order, `PLAYBOOK.md` Frame step and autopilot Phase 1 — a `<…>`
  placeholder is a hard init gate.
- **Status:** in force

## 2026-06-23 — Autopilot's absolute "never touch `main`" contradicted an explicit authorization
- **Source:** same run, on a greenfield repo where the human had authorized committing to `main`.
  The rail was written with no room for the case, so following it meant ignoring the human.
- **Result:** `.claude/skills/autopilot/SKILL.md` — the exception is surfaced and logged rather
  than silently broken.
- **Status:** in force

## 2026-06-23 — Protocol text in `CLAUDE.md` was paid for in every context, including subagents'
- **Source:** same run. Per-project content and protocol had accumulated in the spine, which loads
  everywhere.
- **Result:** `CLAUDE.md` thinned so only "Project specifics" is per-project; the protocol's single
  home is `PLAYBOOK.md`.
- **Status:** in force

## 2026-06-23 — Two autopilot runs on the same day overwrote each other's morning report
- **Source:** same run. `docs/AUTOPILOT-<date>.md` collides when a day has more than one run.
- **Result:** `.claude/skills/autopilot/SKILL.md` — the report path is slugged by branch.
- **Status:** in force
