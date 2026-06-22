# 0001 — Record architecture decisions as ADRs

- **Status:** accepted
- **Date:** <date>

## Context
Across long-running projects, the model (and humans) re-argue settled choices — wasting tokens
and risking drift. Chat history is lost between sessions; a single big "living doc" buries
decisions under noise so nobody can cite them.

## Decision
Record each settled decision as a short, numbered file in `docs/decisions/`. Treat `accepted`
ADRs as **frozen**. Supersede with a new ADR; never re-decide silently.

## Consequences
- Every session reads this folder and respects it.
- Decisions get a stable, citable id (`0001`, `0002`, …).
- `STATE.md` stays small because rationale lives here, not in the state doc.

---
_This first ADR is the example. Copy `../templates/adr-template.md` for the next one._
