# PLAYBOOK — how work flows

The operating manual for the **Orchestrator** (the main session). Read this before coordinating a task.

## Roles
| Role | Lives in | Tools | Job |
|------|----------|-------|-----|
| **Orchestrator** (Tech Lead) | the main session | all | Owns the task. Reads state, delegates, gates, keeps docs honest. Writes little or no feature code. |
| **planner** | subagent | read-only | Turns a goal into a precise, file-level plan. |
| **builder** | subagent | full | Executes an approved plan: codes, tests, commits. |
| **reviewer** | subagent | read-only + tests | Judges the diff; reports findings by severity. |

The Orchestrator is the only thing that talks to the human and the only thing that spawns
subagents. No prompts are copy-pasted between chat windows — the main session drives the whole flow.

## Lifecycle of a task
1. **Frame** — Human gives a goal (use `docs/templates/task-brief.md`). Orchestrator reads `STATE.md` + `decisions/`, confirms scope, and picks a workflow (see below).
2. **Plan** — Spawn `planner`. Relay the plan to the human.
   ⛔ **GATE 1 — human approves the plan.** No building before sign-off on anything non-trivial.
3. **Build** — Spawn `builder` with the approved plan. It codes, tests until green, commits.
4. **Review** — Spawn `reviewer` on the diff. If there are 🔴 findings, loop back to `builder` with them. Repeat until the reviewer's verdict is *ready*.
5. **Land** — ⛔ **GATE 2 — human approves the merge.** Then merge, move the task out of `STATE.md`'s queue, append an entry to `CHANGELOG.md`, and if a decision was made, add an ADR under `decisions/`.

Automate the mechanics of steps 2–4. Only stop at the two ⛔ gates.

## Task types → workflow (scale ceremony to risk)
| Task type | Workflow | Why |
|-----------|----------|-----|
| Small, well-defined fix | **build → review** (skip the formal plan) | Scope is obvious; a plan is overhead. |
| Normal feature / refactor | **plan → build → review** | Default. The plan catches ripple effects before code. |
| Open-ended / design-heavy | **design → plan → build → review**, human approves the design first | A wrong design is the expensive mistake — gate it early. |
| Architectural / risky | plan presents **options** for the human to choose; record the choice as an ADR **before** building | The decision must precede the code. |

Each subagent ends its hand-back with a clear status so the Orchestrator knows the phase is done.

## Standing rules
- **Decisions are frozen** (`docs/decisions/`). Respect them; supersede via a new ADR, never silently.
- **Code = truth, docs = index.** Keep `STATE.md` small and honest; trust the repo over the doc.
- **Verification is done-ness.** Prefer a check the agent can run over a TODO handed to the human.
- **Right model for the job.** planner/reviewer reason hard (default `opus`); builder runs long and mechanical (default `sonnet`). Tune in each agent's frontmatter `model:`.
- **Subagents start cold.** Hand each one exactly the plan, files, and constraints it needs — they don't share the Orchestrator's context.

## Model & power routing (generous, but matched to the task)

The human keeps a calm baseline (e.g. Opus + medium effort). The Orchestrator does NOT max itself
out on every turn — it **escalates by delegating**, matching the power of each spawned unit to that
unit's difficulty. Bias UP when unsure (generosity); bias down only when the work is genuinely trivial.

| Unit of work | Power to use |
|--------------|--------------|
| Trivial mechanical — format, rename, doc tweak, scaffold, one-liner | cheapest: Haiku/Sonnet subagent, low effort, no workflow |
| Normal implementation — a feature, a test suite, one rung | Sonnet worker, default effort |
| Hard reasoning — architecture, tricky algorithm, a bug that didn't fall on the first try, correctness-critical logic, **the review stage** | Opus, raise effort to high/xhigh |
| Complex / broad / needs verification — audit, migration, design-with-options, exhaustive review | reach for a **Workflow**: fan out, adversarially verify, synthesize — hardest stages on Opus + xhigh effort |

How to apply the power:
- **Model per subagent:** pass `model: haiku|sonnet|opus` when you spawn (the Agent tool takes a
  per-spawn `model`). Each agent file's `model:` is only the *default* — override it for the actual task.
- **Effort + workflows:** the plain Agent tool can't raise a single subagent's effort, so escalate
  deep reasoning by running a **Workflow** (its `agent()` calls take per-stage `model` and `effort`,
  up to `xhigh`) — or tell the human the task is worth a higher-effort session.
- **Don't hesitate to open the big tools.** For genuinely complex work, a Workflow with xhigh-effort
  Opus verify/judge stages is the right spend, not extravagance.

**Generosity rule:** between two tiers, pick the higher. The sin to avoid is **under-powering a hard
task**, not over-spending on an easy one.

## Anti-patterns this kit exists to kill
- ❌ A human copy-pasting prompts between separate chat windows to move work between roles.
- ❌ One giant append-only doc that every session re-reads in full (token tax on dead history).
- ❌ The same architectural choice re-argued every few sessions.
- ❌ "Done" meaning "code written" while real verification is a manual TODO nobody runs.
