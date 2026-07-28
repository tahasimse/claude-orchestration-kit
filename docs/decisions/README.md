# Architecture Decisions (ADRs)

Each file here records **one decision that is frozen**: settled, not to be re-litigated by any
agent or session. This is the antidote to "the model keeps re-arguing the same choice."

## Rules
- **One decision per file:** `NNNN-short-slug.md` (zero-padded, incrementing — `0001`, `0002`, …).
- **Write one** whenever a choice is made that future work must respect: an architecture or tech
  pick, a domain rule, a trade-off accepted for delivery.
- **Status lifecycle:** `proposed` → `accepted` → (`superseded by NNNN`).
- **To change a frozen decision,** write a NEW ADR that supersedes the old one. Don't edit history.

## For agents
Read this folder at session start. If your task conflicts with an `accepted` ADR, **stop and
flag it** — do not silently decide otherwise.

Template: [`../templates/adr-template.md`](../templates/adr-template.md).

## Index — this repo's own decisions
| # | Decision | Status |
|---|----------|--------|
| [0001](0001-record-architecture-decisions.md) | Record architecture decisions | accepted |
| [0002](0002-run-the-kit-on-itself.md) | Run the kit on itself; ship the blanks as templates | superseded by 0009 |
| [0003](0003-one-builder-no-layer-split.md) | One Builder, no backend/frontend split | accepted |
| [0004](0004-two-human-gates.md) | Two human gates: plan and merge | accepted |
| [0005](0005-memory-layered-by-lifetime.md) | Memory layered by lifetime, not by topic | accepted |
| [0006](0006-model-and-power-routing.md) | Match model power to the unit of work | accepted |
| [0007](0007-inline-vs-delegate-hybrid-default.md) | Route inline vs delegate by context cost | accepted |
| [0008](0008-license-apache-2-0.md) | Relicense GPL-3.0 → Apache-2.0 | **proposed** |
| [0009](0009-self-host-on-a-snapshot-branch.md) | Keep `main` installable; self-host on a snapshot branch | accepted |

> **This index exists only on the `self-host` branch.** On `main` the folder holds `0001` alone, so
> a copier's frozen-decisions folder starts empty of anyone else's choices and their numbering
> starts at `0002` — see [`0009`](0009-self-host-on-a-snapshot-branch.md). If you did copy from
> here, delete `0002`–`0009` and this index; `0001` is the one worth keeping.
