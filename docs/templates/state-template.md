# STATE — <project name>

> **Template.** Copy this over `docs/STATE.md` in your own project, then fill it in:
> `cp docs/templates/state-template.md docs/STATE.md`.
> The kit's own `docs/STATE.md` is a *live* file, not a blank — read it as the worked example.
>
> ⚠️ **UNINITIALIZED** — this file still has `<…>` placeholders. Until they're filled, the project is
> not oriented: the Orchestrator must reconstruct and populate STATE (branch, one-line state, queue)
> from `git log` / `git status` **before** coordinating any task. Delete this banner once STATE is real.
>
> The single always-read file. Keep it **small**: current state + what's next.
> History goes in `CHANGELOG.md`. Rationale goes in `decisions/`. If this file grows past
> roughly one screen, you're putting history or decisions in the wrong place.

_Last updated: <date> — commit `<hash>`_

## Now
- **Branch:** `<branch>`
- **State:** <one short paragraph — what works, what's in flight, what's broken>

## Work queue (prioritized)
| # | Task | Type | Status | Blocked by |
|---|------|------|--------|------------|
| 1 | <task> | feature / fix / refactor / chore | queued | — |
| 2 | … | … | … | … |

## Known issues / debt
- `<id>` — <short description> — <severity>

## Quick check reference
(Full detail in `CLAUDE.md` → "Project specifics".)
- **Build:** `…`
- **Test:** `…`
- **Run:** `…`
