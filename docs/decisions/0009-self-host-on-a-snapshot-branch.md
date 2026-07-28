# 0009 — Keep `main` installable; self-host on an unmerged snapshot branch

- **Status:** accepted
- **Date:** 2026-07-29
- **Supersedes:** [`0002`](0002-run-the-kit-on-itself.md)

## Context
`0002` resolved the template-versus-project conflict by splitting the two roles **by location**:
blanks to `docs/templates/`, live files at the canonical paths. It assumed that split would land on
`main`. Applied there, it costs more than it looked:

- The install stops being a copy. `cp -R docs .` now delivers this repo's own state, so step 3
  exists only to undo step 2.
- Worse, it delivers this repo's **decisions**. A copier's `docs/decisions/` would arrive holding
  `0002`–`0009` — and `CLAUDE.md` tells their agent that everything in that folder is frozen and
  binding. The kit's own licensing and builder-architecture choices would silently become
  constraints on someone else's project. Their first ADR would also have to start at `0010`.
- `0002` accepted a permanent maintenance obligation on `main`: every change owes a CHANGELOG
  entry, every choice an ADR. On a repo whose primary job is to be copied, that is overhead paid
  forever for a benefit that is read once.

Against that, the benefit `0002` bought is real but narrow: **evidence**. A blank template cannot
show that its own process works. The mistake was assuming evidence has to live on `main`.

Two things settle it. First, the owner's priority is explicit — the repo at `main` must be
downloadable and usable, and the demo is secondary. Second, a branch does not imply an obligation
to maintain it: **a branch can carry an idea.** `full-delegate` already does exactly that for
`0007`, holding the delegate-everything variant that `0007` rejected. A branch is the runnable form
of an "alternatives considered" section.

## Decision
`main` stays a clean, installable template. `docs/STATE.md` and `docs/CHANGELOG.md` are blanks
there, `docs/decisions/` carries only `0001`, and the install is the copy — nothing to reset.

The self-hosting demo lives on **`self-host`**, a frozen snapshot cut from `main` at `0c9ac22`,
deliberately never merged. `main`'s README links to it as evidence. The split into
`docs/templates/` that `0002` introduced stays on that branch, where it is what makes the branch
readable; it does not travel to `main`.

This repo may use branches to carry ideas rather than to stage work. An idea branch is **declared**
— what variant it holds and the commit it was cut from — and is **not** maintained. Falling behind
`main` is expected of it and is not debt. Rebasing an idea branch to chase `main` destroys the
thing it was preserving.

## Consequences
- The install returns to one step. The cost `0002` accepted is not paid.
- No ADR pollution: a copier's frozen-decisions folder holds only `0001`, and their numbering
  starts at `0002`.
- `main` carries no CHANGELOG or ADR obligation, so it cannot visibly lapse.
- The evidence is one click less visible. Most GitHub visitors never leave the default branch, so
  the README link does real work and should be prominent rather than a footnote.
- The snapshot goes stale by design. Anyone reading it is reading the kit as of `0c9ac22`, which
  the branch says on its own README and in its `STATE.md`.
- `full-delegate` is reclassified: not drift to be rebased or retired, but an undeclared idea
  branch that needs a label. Tracked as `branch-labels`.
- Self-hosting stops being continuous. The snapshot proves the process was run once, end to end;
  it does not prove it is run every week. That is a weaker claim, honestly made.

## Alternatives considered
- **Merge `self-host` into `main`** — `0002`'s original plan. Rejected: it trades the repo's
  primary job (be installable) for its secondary one (be evidence), and the ADR-pollution failure
  mode is silent and lands in someone else's project.
- **Keep the demo on `main` under a folder that is not copied** (`self-host/`, `.kit/`) — the
  strongest rival, and better on discoverability: no branch switch, no staleness. Rejected because
  the kit's own live state would then sit at a path its own `CLAUDE.md` read order does not point
  at, requiring a per-repo exception in the one file that is supposed to be uniform. Worth
  revisiting if the README link proves too easy to miss.
- **A tag instead of a branch** (`self-host-demo`) — frozen more literally, and staleness could not
  be mistaken for neglect. Rejected: tags are less discoverable than branches on GitHub, and the
  branch already carries its own snapshot banner.
- **A second repository** — rejected: it splits the story across two places and doubles what has to
  be kept coherent, to solve a problem one README link solves.
