# 0008 — Relicense from GPL-3.0 to Apache-2.0

- **Status:** accepted
- **Date:** 2026-07-29 (accepted by the copyright holder, same day)

## Context
The kit is licensed GPL-3.0. Its entire distribution model, stated in the README's install section,
is "copy `.claude/`, `docs/` and `CLAUDE.md` into your own repo" — the copied files then sit inside
someone else's project tree, next to their source.

That is where GPL-3.0 fits badly:

- **It targets the wrong artifact.** GPL-3.0 is written for programs, in terms of source code,
  object code and "the preferred form for making modifications." This kit is Markdown prose plus a
  234-byte JSON settings file. Applying a program license to a prose kit leaves its central terms
  with no clear referent.
- **It creates friction exactly at adoption.** Copying into a private internal project is not
  distribution, so nothing is triggered — but the person deciding is rarely a lawyer. Seeing a
  copyleft license on files they are being told to paste into their own repo is enough to make a
  team say no, and legal review at a company will ask the question properly and slowly. For a kit
  whose only measure of success is adoption, that friction is the whole cost.
- **Copyleft protects a value that is not at risk here.** GPL exists to keep improvements to a
  program open. Nobody builds a proprietary product out of a PLAYBOOK and three agent files; the
  realistic "harm" is someone adapting the prose privately, which is the intended use anyway.

## Decision
Relicense to **Apache-2.0**: permissive, no obligation on anyone who copies the files into their own
repo, an explicit patent grant, and a `NOTICE`-based attribution mechanism that keeps authorship
visible. Replace `LICENSE`, update the README's License section, and note the change in
`CHANGELOG.md`.

## Consequences
- Removes the adoption blocker: a team can copy the kit in without a licensing conversation.
- Gives up copyleft permanently. Anyone may fork the kit, extend it and ship the result closed.
- **The repackaging scenario is accepted with open eyes.** The argument above — "nobody builds a
  proprietary product out of a PLAYBOOK" — understates the risk, because here the kit *is* the
  product, not a component of one. There is a live market in packaged AI workflow setups, and
  Apache-2.0 lets someone sell this one on with nothing owed but attribution, where GPL-3.0 would
  have forced them to carry the source. That deterrent is given up knowingly: it was never
  enforceable by a solo author anyway, so what was really held was a signal, not a protection —
  and its cost was paid by every honest adopter.
- Prior releases stay available under GPL-3.0 — relicensing is not retroactive, and anyone who
  already took the GPL grant keeps it.
- Requires the copyright holder to act, and requires that all contributions to date are that
  holder's. As of `0c9ac22` every commit is tahasimse's, so there is no third-party consent to
  collect. **This stops being true the moment the first outside PR is merged** — take the decision
  before then, or a CLA becomes necessary.
- Apache-2.0 imposes a small obligation the GPL does not spell out the same way: derivatives must
  state their changes and carry the `NOTICE`. Lighter than copyleft, not zero.

## Alternatives considered
- **Stay GPL-3.0** — rejected: the friction is paid at every adoption, and the protection bought is
  near-worthless for prose. It is the safe choice for a *program*, and this is not one.
- **MIT** — the closest runner-up: shorter and better known than Apache-2.0. Rejected for the
  explicit `NOTICE` mechanism, which is the whole reason to prefer Apache here: it obliges
  attribution to travel *with the copied files*, which is exactly how this kit moves. The patent
  grant is the usual second argument for Apache and is nearly weightless for a Markdown kit —
  there is no patentable invention in a PLAYBOOK. If `NOTICE` did not matter, MIT would win on
  brevity and recognition.
- **CC0 / public domain dedication** — rejected: the right shape for pure prose, but it drops
  attribution entirely, and the `.claude/` config files are close enough to software that a
  software license is the safer container.
- **CC-BY-4.0** — the licence family actually built for prose, and it keeps attribution. Rejected
  for the same reason as CC0: Creative Commons advises against its licences for software, and this
  repo is not purely prose — `.claude/settings.json` and the agent files are configuration that a
  tool executes against. One container for the whole repo, and it should be the one that covers
  the software-shaped part.
- **Dual license (GPL for the kit, permissive for the copied files)** — rejected: needs the reader
  to work out which file falls under which license, which is more friction than the copyleft it
  was meant to preserve.
