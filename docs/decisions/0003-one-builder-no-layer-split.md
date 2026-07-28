# 0003 — One Builder, no backend/frontend split

- **Status:** accepted
- **Date:** 2026-06-23 (recorded retroactively 2026-07-29)

## Context
The obvious way to model a dev team in agents is to mirror its job titles: a backend builder, a
frontend builder, maybe an infra one. The microservice project this kit was distilled from
(Spring services + Angular SPA + Docker/CI) is exactly the shape where that split looks natural.

It did not hold up. Layer-split builders each start cold, each re-derive the design from the same
plan, and each pick their own naming and structure. Where the layers grow together — a DTO and the
component that consumes it, a schema and the parser rung that reads it — the seam between two cold
authors costs more than the parallelism buys. The reviewer then spends its budget on inconsistency
rather than on correctness.

## Decision
One `builder` agent handles every layer. Delegate **one builder per coherent artifact, not per
commit and not per layer**. Split across builders only when the pieces are genuinely independent;
when they grow together, keep a single author and gate with the reviewer at the end.

## Consequences
- Consistent naming and structure across a unit of work, and one context that already knows the
  design instead of three that re-derive it.
- Less parallelism. Two independent features run sequentially unless the Orchestrator explicitly
  spawns two builders, which is allowed but is not the default.
- The builder's context can grow large on a big artifact. The `/autopilot` anti-spin rule and
  auto-compaction are the mitigations; a genuinely huge artifact should be re-scoped into smaller
  ones rather than split by layer.
- The escape hatch stays documented: copy `builder.md` to `backend-builder.md` /
  `frontend-builder.md` if a project really needs it (README → Customizing).

## Alternatives considered
- **backend-builder + frontend-builder** — rejected: cold starts and drift at the seam cost more
  than the parallel throughput, on exactly the polyglot project that motivated the idea.
- **One builder per commit** — rejected: the same drift, at higher frequency. Incremental work
  (a parser growing rung by rung) is where a single author is most clearly better.
