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

## 2026-07-29 — `LESSONS.md`: keep the reason a rule exists ✅
- **Commit(s):** `5b58b7c` on `feat/lessons` (mechanism, for `main`); this branch for the kit's own
  entries and the ADR.
- **What:** The kit recorded chosen reasoning (ADRs) and delivered work (CHANGELOG), but nothing
  recorded *learned* reasoning. The first autopilot run produced seven findings that were applied
  as rules and then survived only in the commit message of `86a01ce`. `LESSONS.md` is the
  append-only half of a pair whose live half is `CLAUDE.md`'s gotchas — the same lifetime split
  `0005` already draws between `STATE.md` and `CHANGELOG.md`. Gotchas gained an admission test
  ("without this, would any agent get its first move wrong?"), a ~5-entry cap and routing for what
  fails the test. Lessons are filtered by "something went wrong **and** something changed", read
  only before changing a rule, and retired rather than deleted. Those seven findings are now
  backfilled here as entries, along with the two lessons from today.
- **Decisions touched:** `0010` (new, accepted). It also records the dropping of the queued
  `RUNLOG.md`: its only purpose was to put numbers behind comparative README claims, which a
  kit-only log cannot do, and the call was that the kit does not have to prove itself.
- **Verified:** `LESSONS.md` reachable from `CLAUDE.md` read order, the PLAYBOOK Land step and the
  new PLAYBOOK section; every backfilled entry names the file its rule actually lives in, checked
  against the repo. `STATE.md` queue no longer references `RUNLOG.md`; `no-numbers` reclassified
  from pending work to accepted limitation.
- **Left for human:** Gate 2 on `feat/lessons` → `main`, and on this branch.

## 2026-07-29 — Relicense GPL-3.0 → Apache-2.0 ✅
- **Commit(s):** `319a893` on `chore/relicense-apache-2-0` (for `main`); this branch for the ADR
  and the docs that referenced the old licence.
- **What:** `LICENSE` replaced with the canonical Apache-2.0 text fetched from apache.org, `NOTICE`
  added so attribution travels with copied files, README's License section rewritten. `0008` moved
  `proposed` → `accepted` and gained two things it had been thin on: the repackaging risk is now
  stated and accepted rather than waved away (here the kit *is* the product, so copyleft did deter
  something real — it was just never enforceable by a solo author), and the Apache-over-MIT reason
  is corrected to `NOTICE` rather than the patent grant, which is near-weightless for a Markdown
  kit. CC-BY-4.0 added to the alternatives.
- **Decisions touched:** `0008` (accepted).
- **Verified:** `LICENSE` is the 11358-byte upstream text, head and tail checked. `NOTICE` ships.
  No remaining reference to GPL as the licence in force: README, `CLAUDE.md` gotchas and `0008`
  all updated; the only mentions left are historical ("prior releases stay GPL-3.0"), which is
  correct — the relicense is not retroactive.
- **Left for human:** none for the licence itself. New queue item: a `CONTRIBUTING` note, since
  the "no third-party consent needed" window closes at the first outside PR.

## 2026-07-29 — Reframe as a snapshot: `main` stays installable, ADR 0009 supersedes 0002 ✅
- **Commit(s):** this branch, on top of `fa4fc0f`
- **What:** `0002` had put the self-hosting split on a path to `main`. Applied there it turns the
  install into copy-then-undo and, less visibly, ships this repo's frozen decisions into a copier's
  project — where `CLAUDE.md` tells their agent those decisions are binding. `0009` supersedes it:
  `main` stays a clean template, and the demo lives here, on `self-host`, cut from `main` at
  `0c9ac22` and never merged. Branch renamed `docs/self-host` → `self-host`, since it holds an idea
  rather than staging work. README gains a snapshot banner pointing installers back to `main`;
  STATE says plainly that it is a snapshot and expects to fall behind.
- **Decisions touched:** `0009` (new, accepted). `0002` marked superseded, text kept — its
  diagnosis of the template-versus-project conflict still stands, only the *where* changed.
- **Verified:** `git merge-base self-host main` = `0c9ac22`, matching what the branch claims.
  `main` is untouched at `0c9ac22` and equal to `origin/main`; nothing pushed. `LICENSE` still
  GPL-3.0.
- **Left for human:** Gate 2 on the one commit `main` does need — the README link to this branch.
  Decide `0008`. Queue #2 (measurement) is still the real gap.

## 2026-07-29 — The kit runs on itself: live STATE, ADRs 0002–0008, backfilled CHANGELOG ✅
- **Commit(s):** `fa4fc0f` on branch `self-host`
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
- **Left for human:** Gate 2 — but **not** a merge. This branch stays unmerged as a snapshot, so
  that `main` keeps a one-step install (`decisions/0002`, revised); what `main` needs is a link to
  it. Decide `0008` — `LICENSE` is untouched and the kit remains GPL-3.0 until that call is made.
  Queue item #2 (measurement) is the next real gap: every efficiency claim in the README is still
  an adjective.

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
