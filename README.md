# Claude Orchestration Kit

A small, portable, project-agnostic setup for running real software projects with Claude Code
as a **team of agents** instead of one chat. Drop it into any repo and you get a four-role
pipeline — Orchestrator, Planner, Builder, Reviewer — with thin, layered project memory that
keeps token cost low and decisions from drifting.

Built from lessons learned coordinating a full microservice project (Angular + Spring + polyglot
persistence + ML bots) entirely through Claude agents. It distills what worked, and fixes what
didn't.

---

## Before you start — Claude Code settings

The kit assumes a few Claude Code settings, mostly so the workflow — and especially the overnight
**`/autopilot`** run — flows without you babysitting it. Flip them in the `/config` menu, or just
pin the file-backed ones by pasting this into your **global** `~/.claude/settings.json`:

```json
{
  "model": "opus",
  "effortLevel": "medium",
  "enableWorkflows": true,
  "autoCompactEnabled": true,
  "fileCheckpointingEnabled": true,
  "permissions": {
    "defaultMode": "acceptEdits",
    "allow": ["Bash", "Read", "Edit", "Write"],
    "deny": [
      "Bash(rm -rf /*)",
      "Bash(rm -rf ~*)",
      "Bash(git push --force*)",
      "Bash(git push -f*)",
      "Bash(git reset --hard*)",
      "Bash(sudo *)"
    ]
  }
}
```

What each one actually buys you:

| Setting | Why it matters here |
|---|---|
| `model: opus` | The Orchestrator's brain. It stays sharp and **delegates** cheaper work to Sonnet/Haiku subagents — you don't pay Opus for everything. |
| `effortLevel: medium` | A calm baseline. The deep thinking gets escalated per task, not cranked on every turn. |
| `enableWorkflows: true` | Lets the Orchestrator fan out into a multi-agent workflow when a task is genuinely big. |
| `defaultMode: acceptEdits` | Stops the constant "can I edit this file?" prompts, so a run isn't blocked every few seconds. |
| `allow` / `deny` | No routine prompts — **but** a safety net: `rm -rf`, force-push, and `sudo` stay blocked even when nobody's watching. |
| `autoCompactEnabled: true` | Long sessions (and overnight runs) don't hit the context wall and die. |
| `fileCheckpointingEnabled: true` | Snapshots before edits — `/rewind` can undo anything an unattended run did. |

A few more are **on by default** — just confirm them in `/config`: **Thinking mode** (reasoning),
the **Ultracode keyword trigger** (type `ultracode` in a prompt to force maximum effort + workflows
for that turn), and **Session recap** (a catch-up summary when you return to a finished autopilot run).

> ⚠️ **Don't reach for `bypassPermissions` if you want the `deny` safety net.** `bypassPermissions`
> skips *all* permission checks, so the `rm -rf` / force-push guards stop applying. `acceptEdits`
> gives you the same "no nagging" feel while keeping the guardrails — which matters most when
> `/autopilot` is running while you sleep.

Everything else (theme, progress bar, turn duration, auto-update channel, …) is personal taste —
the defaults are fine.

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
│   ├── skills/
│   │   └── autopilot/        ← /autopilot: plan once → run unattended to done
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

## Overnight / autonomous mode — `/autopilot`

The default flow has two human gates (plan + merge). For unattended runs — you start it and walk
away, or hand it off before bed — the kit ships a skill that collapses to **one** gate:

```
/autopilot <what you want done>
```

- **Phase 1 — plan + GO.** It reads the state, produces a concrete plan (with options when there's
  a real fork), and **stops for a single GO**. You pick and approve.
- **Phase 2 — unattended.** After GO it runs to completion **without asking again**: it makes the
  most plan-consistent choice on any ambiguity and logs the assumption.

Because nobody is watching (and this pairs with a `bypassPermissions` session), Phase 2 imposes
its own safety rails: **branch-only** (never merges to `main`, never deploys, never does anything
irreversible — the result waits on a branch for your morning review), **tests are the gate** (no
broken tree is ever called "done"), **anti-spin** (a wall that won't fall after ~3 tries gets
logged as blocked and skipped, not chewed on all night), and a **morning report** at
`docs/AUTOPILOT-<date>.md`. A clean early finish is a success — it won't invent make-work.

> Trade-off, stated plainly: you swap the pre-merge human review for unattended throughput. The
> reviewer subagent + green tests are the safety net; the branch is the morning checkpoint.

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
