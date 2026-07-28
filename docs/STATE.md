# STATE — Claude Orchestration Kit

> The single always-read file. Keep it **small**: current state + what's next.
> History goes in `CHANGELOG.md`. Rationale goes in `decisions/`.
>
> Copying the kit into your own project? Start from the blank:
> `cp docs/templates/state-template.md docs/STATE.md`.

_Last updated: 2026-07-29 — branch `self-host`, cut from `main` at `0c9ac22`_

## Now
- **Branch:** `self-host` — **a snapshot, not a work branch.** It shows the kit run on itself, and
  is re-cut from `main` when there is something new to show. It is deliberately **not** merged into `main`, so that
  `main` stays a clean, installable template: there, `docs/STATE.md` and `docs/CHANGELOG.md` are
  blanks and `cp -R docs .` is all the install needs. Expect this branch to fall behind `main`;
  that is what a snapshot does.
- **State:** The kit ships and is usable straight from `main`: four roles (`.claude/agents/`), the
  `/autopilot` skill, the PLAYBOOK protocol, and layered memory. It was field-tested on a real
  autopilot build (calc evaluator, `86a01ce`) and on the microservice project it was distilled
  from. What `main` cannot show is **the kit running on itself** — a blank template structurally
  cannot carry evidence. That is this branch's whole job: the blanks moved to `docs/templates/`,
  this file and `CHANGELOG.md` were populated for real, and the standing choices were frozen as
  ADRs `0002`–`0010`. `LESSONS.md` carries what the kit learned by going wrong — the first
  autopilot run's findings had until now survived only in a commit message. Nothing is broken;
  no code, no build.

## Work queue (prioritized)
| # | Task | Type | Status | Blocked by |
|---|------|------|--------|------------|
| 1 | `LESSONS.md` — mechanism on `main`, this repo's own entries here (`decisions/0010`) | feature | awaiting Gate 2 | human |
| 2 | Add a `CONTRIBUTING` note: contributions land under Apache-2.0, so a later licence change would need every contributor's consent | docs | queued | — |
| 3 | Reconcile the permission story: README recommends `acceptEdits` for the deny-net, `/autopilot` assumes `bypassPermissions` | fix | queued | — |
| 4 | Ship a redacted sample `docs/AUTOPILOT-<date>.md` so the morning report is visible before you run one | docs | queued | — |

## Known issues / debt
- `perm-story` — README's settings section argues against `bypassPermissions` (it drops the `rm -rf` / force-push deny-net); `/autopilot` line 15 and 57 assume a `bypassPermissions` session. Both positions are defensible, but the kit currently states both without resolving. — **medium** (queue #3)
- `no-numbers` — Every efficiency claim ("keeps token cost low", "cold starts: none") is unmeasured,
  and **unmeasured by choice**: the claims are comparative, so proving them needs the same task run
  with and without the kit, which is an experiment rather than a log. The copyright holder's call
  was that the kit does not have to prove itself, so the `RUNLOG.md` that existed only to produce
  that proof was dropped (`decisions/0010`). Read the README's comparison table as stated design
  intent, not as a benchmark. — **accepted limitation**, not pending work
- ~~`branch-labels`~~ — closed 2026-07-29: `main`'s README now declares both idea branches, what
  each carries and that being behind `main` is expected of them.

## Quick check reference
(Full detail in `CLAUDE.md` → "Project specifics".)
- **Build:** none — the kit is Markdown + Claude Code config.
- **Test:** copy `.claude/`, `docs/`, `CLAUDE.md` into a scratch repo, run `claude`, confirm the
  session reads `docs/STATE.md` first and that `planner` / `builder` / `reviewer` / `/autopilot`
  are all discovered.
- **Run:** `claude` from a project root that has the three copied paths.
