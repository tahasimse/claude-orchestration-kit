# STATE — Claude Orchestration Kit

> The single always-read file. Keep it **small**: current state + what's next.
> History goes in `CHANGELOG.md`. Rationale goes in `decisions/`.
>
> Copying the kit into your own project? Start from the blank:
> `cp docs/templates/state-template.md docs/STATE.md`.

_Last updated: 2026-07-29 — branch `docs/self-host`, head `fa4fc0f`_

## Now
- **Branch:** `docs/self-host` (off `main`, awaiting Gate 2)
- **State:** The kit ships and is usable: four roles (`.claude/agents/`), the `/autopilot` skill,
  the PLAYBOOK protocol, and layered memory. It was field-tested once on a real autopilot build
  (calc evaluator, `86a01ce`) and on the microservice project it was distilled from. What was
  missing until now is **the kit running on itself** — STATE was a placeholder, no ADR had been
  written, and CHANGELOG held only its own template. Done on this branch (`fa4fc0f`): distributed
  blanks split into `docs/templates/`, this file populated, CHANGELOG backfilled from `git log`,
  and the seven standing choices frozen as ADRs `0002`–`0008`. Waiting on the human to merge, and
  to decide `0008`. Nothing is broken; no code, no build.

## Work queue (prioritized)
| # | Task | Type | Status | Blocked by |
|---|------|------|--------|------------|
| 1 | Merge `docs/self-host` into `main` | chore | awaiting Gate 2 | human |
| 2 | Run log + measurement: record review rounds, retries and token cost per task in `docs/RUNLOG.md`, so README claims stop being adjectives | feature | queued | 1 |
| 3 | Decide ADR `0008` (relicense GPL-3.0 → Apache-2.0) | chore | proposed | human |
| 4 | Rebase or retire `full-delegate` — it has not moved since the inline-vs-delegate change on `main` | chore | queued | 1 |
| 5 | Reconcile the permission story: README recommends `acceptEdits` for the deny-net, `/autopilot` assumes `bypassPermissions` | fix | queued | — |
| 6 | Ship a redacted sample `docs/AUTOPILOT-<date>.md` so the morning report is visible before you run one | docs | queued | 2 |

## Known issues / debt
- `perm-story` — README's settings section argues against `bypassPermissions` (it drops the `rm -rf` / force-push deny-net); `/autopilot` line 15 and 57 assume a `bypassPermissions` session. Both positions are defensible, but the kit currently states both without resolving. — **medium** (queue #5)
- `no-numbers` — Every efficiency claim ("keeps token cost low", "cold starts: none") is unmeasured. — **medium** (queue #2)
- `branch-drift` — `origin/full-delegate` predates `0c9ac22`; divergence unverified. — **low** (queue #4)

## Quick check reference
(Full detail in `CLAUDE.md` → "Project specifics".)
- **Build:** none — the kit is Markdown + Claude Code config.
- **Test:** copy `.claude/`, `docs/`, `CLAUDE.md` into a scratch repo, run `claude`, confirm the
  session reads `docs/STATE.md` first and that `planner` / `builder` / `reviewer` / `/autopilot`
  are all discovered.
- **Run:** `claude` from a project root that has the three copied paths.
