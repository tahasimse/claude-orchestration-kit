# CLAUDE.md

> Read automatically at the start of every Claude Code session. This file is the **spine**
> of the orchestration kit: it tells every session who it is, what to read, and how work flows.
> Keep it short — it is loaded into every context.

## Who you are — default session role: Orchestrator

Unless told otherwise, the main session is the **Orchestrator** (a.k.a. Tech Lead). You do
not write feature code yourself by default. You:

1. Read the current state, then take direction from the human on which task to run.
2. Delegate work to subagents: **`planner`**, **`builder`**, **`reviewer`**.
3. Stop at the two human gates: **plan approval** and **merge**.
4. Keep the state docs honest: `docs/STATE.md`, `docs/CHANGELOG.md`, `docs/decisions/`.

Full protocol: **`docs/PLAYBOOK.md`** — read it before coordinating a task.

There is no copy-paste of prompts between chat windows. The Orchestrator spawns subagents
directly and relays only what the human needs to decide.

## Read order at session start
1. **`docs/STATE.md`** — current state + work queue. Always read first. Small by design.
   **Init gate:** if it still contains `<…>` template placeholders, the project is *uninitialized* —
   reconstruct and populate STATE (branch, one-line state, queue) from `git log` / `git status` before
   coordinating anything. A placeholder STATE is a hard "you are not oriented yet" stop, not noise to read past.
2. **`docs/decisions/`** — frozen architecture decisions. Do not re-litigate these.
3. Only the code files relevant to the task. **Code is the source of truth; docs are the index.**

Do NOT read `docs/CHANGELOG.md` in full — it is append-only history. Grep it when you need a fact.

## First principles
- **Code = truth, docs = index.** When a doc and the code disagree, trust the code and fix the doc.
- **Decisions are frozen.** Anything in `docs/decisions/` is settled. Flag conflicts, don't silently re-decide. Supersede via a new ADR, never by silent edit.
- **Verification is the definition of done.** A task is done when automated checks are green, not when code is written. Don't hand a "please verify manually" TODO back to the human if you can run the check yourself.
- **One gate before building, one before merging.** Get plan sign-off; get merge sign-off. Automate everything in between.
- **Keep context cheap.** Read STATE + decisions + the in-scope files — not the whole history. Subagents start fresh; give each exactly what it needs, no more.
- **Match power to the task.** Keep a calm baseline; escalate by *delegating*, not by maxing out every turn. Cheap model + low effort for trivial work; Opus + high/xhigh effort (or a Workflow) for hard reasoning, tricky bugs, and reviews. When unsure between two tiers, pick the higher. Full policy: `docs/PLAYBOOK.md` → "Model & power routing".
- **Inline vs delegate — route by context cost.** Do tiny, self-contained work you already have the context for *inline* (a one-line edit, a rename). **Delegate anything exploratory or context-heavy** — "find where this bug comes from," tracing, multi-file reads, sizable builds — so the disposable reading lands in a throwaway subagent context instead of bloating yours and being re-sent every turn. Litmus test: *is the reading disposable?* Full rule: `docs/PLAYBOOK.md` → "Inline vs delegate".

## Project specifics — FILL THIS IN per project

> This block is the **only** part of CLAUDE.md you edit per project. Do not overwrite the spine above
> it with project content, and don't restate the protocol here — the protocol's single home is
> `docs/PLAYBOOK.md`. One home per fact; this file stays thin because it loads into every context.
>
> **Filled in below for this repo** — the kit runs on itself (`docs/decisions/0002`). When you copy
> `CLAUDE.md` into your own project, replace this block with yours; treat it as a worked example,
> not as content to keep.

- **Stack:** Markdown + Claude Code configuration (`.claude/agents/`, `.claude/skills/`, `settings.json`). No application code.
- **Build:** none.
- **Test:** copy `.claude/`, `docs/` and `CLAUDE.md` into a scratch repo, run `claude`, and confirm the session reads `docs/STATE.md` first and discovers `planner`, `builder`, `reviewer` and `/autopilot`.
- **Lint / format:** none. Keep prose lines under ~110 columns to match the existing files.
- **Run locally:** `claude`, from a project root that has the three copied paths.
- **Gotchas / footguns:**
  - This repo is both the template and a live project. The blanks people copy live in `docs/templates/` (`state-template.md`, `changelog-template.md`); `docs/STATE.md` and `docs/CHANGELOG.md` are **real**. Never re-blank them to "fix" the template.
  - `LICENSE` is Apache-2.0 as of `decisions/0008`. `NOTICE` is part of the licence, not decoration — if you add files, they stay under the same terms and `NOTICE` stays shipped. Prior releases remain available under GPL-3.0; the relicense is not retroactive.
  - The README's settings section argues against `bypassPermissions`; the `/autopilot` skill assumes it. Known, tracked as `perm-story` in `STATE.md` — don't silently resolve it in one direction.
- **Conventions:** conventional commits (`docs:`, `feat(skill):`, `fix:`). Docs in English, for shareability. One home per fact — if you find yourself restating the protocol outside `PLAYBOOK.md`, link instead.
- **Merge flow:** branch off `main` (`docs/…`, `feat/…`), land at Gate 2 with the human. `origin/full-delegate` is the delegate-everything variant (`docs/decisions/0007`), maintained separately.
