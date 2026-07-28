# 0002 — Run the kit on itself; ship the blanks as templates

- **Status:** accepted
- **Date:** 2026-07-29

## Context
The kit is a process: thin always-read state, frozen decisions, an append-only history. Until now
this repo did not follow it. `docs/STATE.md` carried its own `<…>` placeholders and the
**UNINITIALIZED** banner, `docs/CHANGELOG.md` contained nothing but its own entry template, and
`docs/decisions/` held only `0001`, the ADR about writing ADRs. Seven commits of real design work
had produced no recorded decision.

That is not only a credibility problem for a reader. It is a functional one: per `CLAUDE.md`, a
placeholder STATE is a hard init gate, so any Claude session opened in this repo was told to stop
and get oriented before doing anything.

The reason it stayed that way is a genuine conflict. The repo is simultaneously the **template you
copy** and a **project that is worked on**. One file cannot be both a blank for the copier and a
live record for the maintainer.

## Decision
Split the two roles by location. `docs/STATE.md` and `docs/CHANGELOG.md` at their normal paths are
the kit's **live** files, filled in and maintained like any other project's. The pristine blanks
move to `docs/templates/state-template.md` and `docs/templates/changelog-template.md`, and the
install instructions gain two `cp` lines that reset them in the target project. `CLAUDE.md` keeps
its single copy: its "Project specifics" block is filled in for this repo and labelled as an
example to overwrite. From here on, work in this repo goes through the PLAYBOOK — branch, ADR when
a choice is made, CHANGELOG entry when it lands.

## Consequences
- The repo becomes its own demo. A filled STATE, seven ADRs and a real CHANGELOG are stronger
  evidence that the process works than any README paragraph, and a new user gets a worked example
  instead of a blank form.
- The init gate stops firing on this repo, so a session opened here can actually do work.
- Cost: two extra `cp` lines in the install path, and one more thing that can be forgotten. A
  copier who skips them inherits the kit's state as their own — the templates carry a banner
  saying so, but the failure mode is real.
- Maintenance cost is now permanent. Every change to this repo owes a CHANGELOG entry, and every
  choice owes an ADR. If that lapses the repo argues against itself, more visibly than before.

## Alternatives considered
- **Keep the blanks at the canonical paths, put the kit's own live state under `docs/self/`** —
  rejected: the first file a reader opens is still `docs/STATE.md`, so it would still greet them
  with UNINITIALIZED, which is exactly the problem being fixed.
- **Rename the shipped files to `STATE.template.md` and have the installer rename them** —
  rejected: it changes the canonical path everything else refers to (`CLAUDE.md` read order, the
  agent files, the PLAYBOOK) for a copier who has not run the install yet.
- **Leave it alone and explain in the README that the kit is a template, not a project** —
  rejected: it is the honest description, but it forfeits the strongest thing the repo could show,
  and it leaves the init gate tripping in its own repo.
