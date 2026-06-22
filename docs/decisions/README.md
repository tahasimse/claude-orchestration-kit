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
