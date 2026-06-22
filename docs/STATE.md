# STATE — <project name>

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
