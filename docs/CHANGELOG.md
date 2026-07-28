# CHANGELOG

> Append-only record of delivered work. One entry per completed task, newest on top.
> This file is **not** read in full at session start — grep it when you need history.
> Loosely follows [Keep a Changelog](https://keepachangelog.com); adapt as you like.
>
> Copying the kit into your own project? Start from the blank:
> `cp docs/templates/changelog-template.md docs/CHANGELOG.md`.
>
> **Entries dated 2026-06-23/25 were backfilled on 2026-07-29** from `git log`, before the kit
> started running on itself (see `decisions/0002`). Their "Verified" lines say what can be
> checked from the repo today, not what was checked at the time — which was nothing formal.

<!-- New entries go directly below this line. -->

## 2026-07-29 — The kit runs on itself: live STATE, ADRs 0002–0008, backfilled CHANGELOG ✅
- **Commit(s):** branch `docs/self-host`
- **What:** Resolved the template-versus-project conflict that had kept this repo from following
  its own process. The distributed blanks moved to `docs/templates/state-template.md` and
  `changelog-template.md`; `docs/STATE.md` and `docs/CHANGELOG.md` at their canonical paths are now
  live and filled. Wrote the seven decisions the kit already makes but had never recorded, and
  backfilled this file from `git log`. `CLAUDE.md` → "Project specifics" filled in for this repo as
  a worked example. README install gains two `cp` lines that reset the two live files in a target
  project.
- **Decisions touched:** `decisions/0002` (new, accepted — the split and the self-hosting rule);
  `0003`–`0007` (new, accepted — retroactive records of choices already in force);
  `0008` (new, **proposed** — relicense to Apache-2.0, awaiting the copyright holder).
- **Verified:** `docs/STATE.md` no longer contains `<…>`, so the init gate in `CLAUDE.md` and the
  PLAYBOOK Frame step stops firing on this repo. `docs/templates/state-template.md` still carries
  the placeholders and the UNINITIALIZED banner, so a copier still gets the gate. Manual read-through
  of the install path against the new layout.
- **Left for human:** Gate 2 (merge `docs/self-host` into `main`). Decide `0008` — `LICENSE` is
  untouched and the kit remains GPL-3.0 until that call is made. Queue item #2 (measurement) is the
  next real gap: every efficiency claim in the README is still an adjective.

## 2026-06-25 — Route inline vs delegate by context cost ✅
- **Commit(s):** `0c9ac22`
- **What:** Named the hybrid that had been implicit: do work inline when you already hold the
  context and it takes a couple of lines; delegate anything exploratory or context-heavy so the
  disposable reading lands in a throwaway context. Litmus test — *is the reading disposable?*
  Added as a first principle in `CLAUDE.md` and a full section in the PLAYBOOK. Established
  `main` as the context-aware default and `full-delegate` as the delegate-everything extreme.
- **Decisions touched:** recorded retroactively as `decisions/0007`.
- **Verified:** docs only, no behavior to run. Read-through.
- **Left for human:** `full-delegate` was not rebased and has not moved since. Tracked as
  `branch-drift` in `STATE.md`.

## 2026-06-23 — README: full "Get started — download, install, run" ✅
- **Commit(s):** `69932c2`
- **What:** Replaced a terse install note with the end-to-end path: clone, copy the three things
  (`.claude/`, `docs/`, `CLAUDE.md`), the resulting tree, a merge warning for projects that already
  have a `.claude/` or `CLAUDE.md`, the two files to fill in, and the first command to hand the
  Orchestrator.
- **Decisions touched:** none.
- **Verified:** commands read through against the repo layout; not executed end-to-end.
- **Left for human:** none.

## 2026-06-23 — README: "Before you start — Claude Code settings" baseline ✅
- **Commit(s):** `399cba3`
- **What:** Documented the settings the kit assumes, as a pasteable global `settings.json`, with a
  table saying what each one buys. Includes the explicit warning against reaching for
  `bypassPermissions`: it skips all permission checks, so the `rm -rf` / force-push / `sudo` deny
  entries stop applying — `acceptEdits` gives the same "no nagging" feel and keeps the guardrails.
- **Decisions touched:** none.
- **Verified:** JSON read through; not applied to a clean machine.
- **Left for human:** this warning sits against `/autopilot`, which assumes a `bypassPermissions`
  session. Unresolved — tracked as `perm-story` in `STATE.md`.

## 2026-06-23 — Field-test refinements from the first real autopilot build (calc evaluator) ✅
- **Commit(s):** `86a01ce`
- **What:** First end-to-end run of the kit on a greenfield build, with seven findings applied:
  (1) a placeholder `STATE.md` now means *uninitialized* — a hard init gate in the read order, the
  PLAYBOOK Frame step and autopilot Phase 1, not noise to read past; (2) autopilot's absolute
  "never main" contradicted an explicit greenfield authorization, so the exception is now surfaced
  and logged rather than silently broken; (3) delegate per coherent artifact, not per commit —
  cold per-commit builders drift on tightly-coupled work; (4) the reviewer must **execute**
  adversarial inputs, since its one real find came from running the program, not reading the diff
  (the highest-value change of the run); (5) review cadence scales with risk — one batch review for
  low-risk coupled rungs, estimated at roughly a fifth the token cost of per-unit review; (6)
  `CLAUDE.md` thinned so only "Project specifics" is per-project; (7) morning-report path slugged
  by branch to avoid same-day collisions. Explicitly kept unchanged after the run: the reviewer
  role, branch-only with a single morning merge gate, one-commit-per-rung with tests as the gate,
  and one sharp reviewer over a fan-out.
- **Decisions touched:** feeds `decisions/0003` (one builder / per-artifact delegation) and
  `0004` (gates and the autopilot exception).
- **Verified:** by the run itself — the kit was used to build a calculator evaluator end to end.
  Nothing from that run was instrumented, so the "~1/5" figure is an impression, not a measurement.
- **Left for human:** the run produced no retained artifacts in this repo. Queue item #2 exists to
  fix that: record review rounds, retries and token cost per task so the next claim has a number
  behind it.

## 2026-06-23 — `/autopilot`: two-phase autonomous run ✅
- **Commit(s):** `500e483`
- **What:** Added the skill that collapses the two gates to one. Phase 1 plans and stops for a
  single GO (with options where there is a real fork); Phase 2 runs unattended, making the most
  plan-consistent choice on ambiguity and logging the assumption. Rails: branch-only, tests as the
  definition of done, anti-spin after roughly three failed attempts, and a morning report at
  `docs/AUTOPILOT-<date>.md`.
- **Decisions touched:** recorded retroactively as part of `decisions/0004`.
- **Verified:** not at the time. First real exercise came the same day in `86a01ce`.
- **Left for human:** the trade is stated in the README rather than hidden — unattended throughput
  bought with the pre-merge human review.

## 2026-06-23 — Generous model & power routing policy ✅
- **Commit(s):** `ace312f`
- **What:** Wrote down how to spend model power: a calm human baseline, escalation by *delegating*
  rather than maxing out every turn, a table mapping unit-of-work difficulty to model and effort,
  and the generosity rule — between two tiers, pick the higher, because under-powering a hard task
  is the worse error. Applied across `CLAUDE.md`, the PLAYBOOK and the autopilot skill.
- **Decisions touched:** recorded retroactively as `decisions/0006`.
- **Verified:** docs only. Read-through.
- **Left for human:** none.

## 2026-06-23 — Initial release ✅
- **Commit(s):** `70bfa32`
- **What:** The kit: `CLAUDE.md` as the spine, `docs/PLAYBOOK.md` as the protocol, the four roles
  (Orchestrator in the main session plus `planner` / `builder` / `reviewer` subagents), memory
  layered by lifetime (`STATE.md` / `decisions/` / `CHANGELOG.md`), the ADR mechanism with `0001`,
  templates, a read-only permission allowlist, and GPL-3.0.
- **Decisions touched:** `decisions/0001` (record architecture decisions). The layering itself was
  recorded retroactively as `0005`, the license as context for `0008`.
- **Verified:** not at the time.
- **Left for human:** the repo shipped without following its own process — placeholder STATE,
  template-only CHANGELOG, no ADR beyond `0001`. Fixed on 2026-07-29 (top entry).
