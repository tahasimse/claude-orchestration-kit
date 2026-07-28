# STATE — Claude Orchestration Kit

> The single always-read file. Keep it **small**: current state + what's next.
> History goes in `CHANGELOG.md`. Rationale goes in `decisions/`.
>
> Copying the kit into your own project? Start from the blank:
> `cp docs/templates/state-template.md docs/STATE.md`.

_Last updated: 2026-07-29 — branch `self-host`, cut from `main` at `0c9ac22`_

## Now
- **Branch:** `self-host` — **a frozen snapshot, not a work branch.** It shows the kit run on
  itself, cut from `main` at `0c9ac22`. It is deliberately **not** merged into `main`, so that
  `main` stays a clean, installable template: there, `docs/STATE.md` and `docs/CHANGELOG.md` are
  blanks and `cp -R docs .` is all the install needs. Expect this branch to fall behind `main`;
  that is what a snapshot does.
- **State:** The kit ships and is usable straight from `main`: four roles (`.claude/agents/`), the
  `/autopilot` skill, the PLAYBOOK protocol, and layered memory. It was field-tested on a real
  autopilot build (calc evaluator, `86a01ce`) and on the microservice project it was distilled
  from. What `main` cannot show is **the kit running on itself** — a blank template structurally
  cannot carry evidence. That is this branch's whole job: the blanks moved to `docs/templates/`,
  this file and `CHANGELOG.md` were populated for real, and the seven standing choices were frozen
  as ADRs `0002`–`0008`. Nothing is broken; no code, no build.

## Work queue (prioritized)
| # | Task | Type | Status | Blocked by |
|---|------|------|--------|------------|
| 1 | Link this snapshot from `main`'s README, so the evidence is reachable without `main` carrying it | docs | awaiting Gate 2 | human |
| 2 | Run log + measurement: record review rounds, retries and token cost per task in `docs/RUNLOG.md`, so README claims stop being adjectives | feature | queued | — |
| 3 | Add a `CONTRIBUTING` note: contributions land under Apache-2.0, so a later licence change would need every contributor's consent | docs | queued | — |
| 4 | Declare `full-delegate` as an idea branch — state the variant it carries and its cut point, instead of rebasing it | docs | queued | 1 |
| 5 | Reconcile the permission story: README recommends `acceptEdits` for the deny-net, `/autopilot` assumes `bypassPermissions` | fix | queued | — |
| 6 | Ship a redacted sample `docs/AUTOPILOT-<date>.md` so the morning report is visible before you run one | docs | queued | 2 |

## Known issues / debt
- `perm-story` — README's settings section argues against `bypassPermissions` (it drops the `rm -rf` / force-push deny-net); `/autopilot` line 15 and 57 assume a `bypassPermissions` session. Both positions are defensible, but the kit currently states both without resolving. — **medium** (queue #5)
- `no-numbers` — Every efficiency claim ("keeps token cost low", "cold starts: none") is unmeasured. — **medium** (queue #2)
- `branch-labels` — This repo uses branches to **carry ideas**, not only to stage work: `self-host`
  is the kit run on itself, `full-delegate` is the delegate-everything variant that ADR `0007`
  rejected. Neither needs maintaining, and neither being behind `main` is a defect. What is missing
  is the label: nothing tells a visitor which branches are deliberate variants and which would be
  abandoned work. Declare the idea and the cut point; do not rebase. — **low** (queue #4)

## Quick check reference
(Full detail in `CLAUDE.md` → "Project specifics".)
- **Build:** none — the kit is Markdown + Claude Code config.
- **Test:** copy `.claude/`, `docs/`, `CLAUDE.md` into a scratch repo, run `claude`, confirm the
  session reads `docs/STATE.md` first and that `planner` / `builder` / `reviewer` / `/autopilot`
  are all discovered.
- **Run:** `claude` from a project root that has the three copied paths.
