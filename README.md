# Claude Orchestration Kit

A small, portable, project-agnostic setup for running real software projects with Claude Code
as a **team of agents** instead of one chat. Drop it into any repo and you get a four-role
pipeline — Orchestrator, Planner, Builder, Reviewer — with thin, layered project memory that
keeps token cost low and decisions from drifting.

Built from lessons learned coordinating a full microservice project (Angular + Spring + polyglot
persistence + ML bots) entirely through Claude agents. It distills what worked, and fixes what
didn't.

---

## Why this exists

The naive way to use a coding agent is one long chat: "build me X." It loses context, re-argues
settled decisions, and produces unverified code.

The *next* instinct — which is where a lot of people land — is to spin up several role chats
(planner, builder, reviewer) and **copy-paste prompts between them by hand**. That works, but
**you become the message bus**: every hand-off is manual, every new chat starts cold and re-reads
a giant status doc, and the same architecture choices get re-litigated.

This kit removes the manual bus. The **main session is the Orchestrator** and spawns the other
roles as Claude Code subagents directly. You stay in the loop only at the two moments that
actually need a human: **approving the plan** and **approving the merge**.

| | Single chat | Manual role chats | This kit |
|---|---|---|---|
| Role separation | none | yes | yes |
| Hand-offs | n/a | manual copy-paste | automatic (subagents) |
| Cold starts | constant | every hand-off | none within a task |
| Decisions kept | no | in your head | frozen ADRs |
| Context cost | grows unbounded | re-reads big doc each time | thin always-read state |
| Human in loop | everywhere | everywhere | two gates only |

---

## Architecture

```
                          ┌──────────────────────────────┐
        you ──goal──▶     │   ORCHESTRATOR (main session) │   ◀── reads STATE + decisions
                          │   "Tech Lead" — drives flow   │
                          └───────────────┬───────────────┘
                                          │ spawns subagents
            ┌─────────────────────────────┼─────────────────────────────┐
            ▼                             ▼                              ▼
      ┌───────────┐  approved plan  ┌───────────┐   diff + findings ┌───────────┐
      │  planner  │ ───────────────▶│  builder  │ ─────────────────▶│ reviewer  │
      │ read-only │                 │ full tools│ ◀──── fixes ──────│ read-only │
      │  (opus)   │                 │ (sonnet)  │     loop until ok  │  (opus)   │
      └───────────┘                 └───────────┘                    └───────────┘
            │                             │                              │
            └────── ⛔ GATE 1: you ───────┘                  ⛔ GATE 2: you ──┘
                    approve the plan                          approve the merge
```

### The four roles
- **Orchestrator** — the main session. Owns the task, reads state, spawns the workers, keeps the
  docs honest. Writes little or no feature code. Defined by `CLAUDE.md` + `docs/PLAYBOOK.md`.
- **planner** (`.claude/agents/planner.md`) — read-only architect. Turns a goal into a file-level
  plan with impact analysis and a test plan. No code.
- **builder** (`.claude/agents/builder.md`) — full-tools implementer. One Builder for all layers
  (no backend/frontend split). Codes, tests until green, commits.
- **reviewer** (`.claude/agents/reviewer.md`) — read-only critic. Reviews the diff, runs tests,
  reports findings by severity. Reports, doesn't fix.

---

## What's in the box

```
claude-orchestration-kit/
├── README.md                 ← you are here
├── CLAUDE.md                 ← the spine: read every session. Has a "fill this in" block.
├── .claude/
│   ├── agents/
│   │   ├── planner.md        ← read-only, opus
│   │   ├── builder.md        ← full tools, sonnet
│   │   └── reviewer.md       ← read-only + tests, opus
│   └── settings.json         ← safe read-only allowlist (fewer permission prompts)
└── docs/
    ├── PLAYBOOK.md           ← the orchestration protocol + task-type workflows + gates
    ├── STATE.md              ← live state + work queue. SMALL. Read first, every session.
    ├── CHANGELOG.md          ← append-only history. Never read in full; grep it.
    ├── decisions/            ← frozen ADRs — settled choices, never re-litigated
    │   ├── README.md
    │   └── 0001-record-architecture-decisions.md
    └── templates/
        ├── adr-template.md
        └── task-brief.md
```

### The memory is layered by lifetime
The biggest token leak in long projects is one ever-growing status doc that every session
re-reads. Here, state is split by how often it changes and who needs it:

| File | Lifetime | Read when |
|------|----------|-----------|
| `STATE.md` | changes constantly, stays small | **every session, first** |
| `decisions/` | append rarely, frozen forever | every session (cheap) |
| `CHANGELOG.md` | append-only, grows forever | only when you need a past fact (grep) |
| `PLAYBOOK.md` | rarely changes | when orchestrating |

Rule of thumb baked into the agents: **code = truth, docs = index.** When they disagree, the
code wins and the doc gets fixed.

---

## Install into a project

From the repo you want to run with agents:

```bash
# from your project root
cp -R /path/to/claude-orchestration-kit/.claude .
cp -R /path/to/claude-orchestration-kit/docs .
cp /path/to/claude-orchestration-kit/CLAUDE.md .   # or merge into an existing CLAUDE.md
```

Then do the one-time setup:
1. Open `CLAUDE.md` and fill in the **"Project specifics"** block — build/test/lint/run commands,
   gotchas, commit + branch conventions. This is what stops every agent from rediscovering how to
   build your project.
2. Fill in `docs/STATE.md` — current branch, one-paragraph state, and the work queue.
3. Optional: write your first real ADR in `docs/decisions/` for any choice already settled.

That's it. `.claude/agents/*` are picked up automatically by Claude Code.

---

## Run a task

In the project, start Claude Code in the main session and just direct it:

```
We're doing the task at the top of STATE.md. Coordinate it per the PLAYBOOK.
```

What happens:
1. **Plan** — the Orchestrator spawns `planner`; you get a file-level plan back.
   → **You approve or adjust it.** (Gate 1)
2. **Build** — it spawns `builder` with the approved plan; code + tests + a commit come back.
3. **Review** — it spawns `reviewer` on the diff; 🔴 findings loop back to the builder automatically.
4. **Land** — you approve the merge (Gate 2); STATE.md and CHANGELOG.md get updated, an ADR is
   added if a decision was made.

You can also drive a single role directly, e.g. *"have the reviewer look at the current diff"* or
*"ask the planner to scope X."*

### Scale the ceremony to the risk
The PLAYBOOK maps task types to workflows so small work stays light:

| Task | Workflow |
|------|----------|
| small, obvious fix | build → review |
| normal feature/refactor | plan → build → review |
| open-ended / design-heavy | design → **plan** → build → review (approve design first) |
| architectural / risky | planner presents options → record an ADR → build |

---

## Design principles (the "why")

- **Stay in the loop where judgment matters, automate the rest.** Two human gates, not ten manual hand-offs.
- **Freeze decisions.** Settled choices live in `decisions/` and are off the table until a new ADR supersedes them.
- **Verification is the definition of done.** "Done" means green checks the agent ran, not code it wrote with a "please test this" note attached.
- **Keep context cheap.** Small always-read state; history is grep-only; subagents get exactly what they need.
- **Right model for the job.** Reasoning-heavy short roles (planner, reviewer) default to `opus`; the long mechanical role (builder) defaults to `sonnet`. Tune per-agent in the frontmatter.

---

## Customizing

- **Add a role** — drop another `.claude/agents/<name>.md`. Give it a tight `description` (that's
  how the Orchestrator decides to spawn it) and restrict `tools` to what it needs.
- **Change model routing** — edit each agent's `model:` (`opus` / `sonnet` / `haiku` / `inherit`).
- **Split the builder again** — if a project really needs it, copy `builder.md` to
  `backend-builder.md` / `frontend-builder.md` and scope each. (This kit merges them on purpose:
  one builder is simpler and the split rarely earns its overhead.)
- **Language** — written in English for shareability; localize the docs/agents freely. Keep
  `CLAUDE.md` and `STATE.md` in whatever language your team thinks in.

---

## License

GPL-3.0 — copyleft. You may use, study, share, and modify this kit, but any
distributed derivative must also be released under the GPL-3.0 and keep its source open.
See [`LICENSE`](LICENSE) for the full text.

Copyright (C) 2026 tahasimse. This program is free software: you can redistribute it
and/or modify it under the terms of the GNU General Public License as published by the
Free Software Foundation, either version 3 of the License, or (at your option) any later
version. It is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY.
