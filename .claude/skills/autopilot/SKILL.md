---
name: autopilot
description: >-
  Two-phase autonomous run for unattended (e.g. overnight) work. Phase 1 turns the user's
  request into a concrete plan — with options when there's a real fork — and STOPS for a single
  GO. Phase 2, after GO, executes to completion with no further questions: branch-only, tests as
  the gate, incremental commits, anti-spin loop bounds, and a morning report. Use when the user
  says "do this overnight", "autopilot", "go and finish without asking", or starts the skill with
  a task to hand off.
---

# Autopilot — plan once, then run unattended to done

Runs in **two phases** with exactly **one** human gate between them. Built for an unattended
session (the user has stepped away / gone to bed) where permission prompts won't block. Because
no human is watching during Phase 2, you impose strict safety rails on yourself.

---

## Phase 1 — Plan and get GO  (you ASK; you do NOT build yet)

1. Take the user's request (their message / the skill args) as the goal.
2. Read context: `docs/STATE.md` and `docs/decisions/` **if they exist**, plus the code files in
   scope. Run `git status` and `git log --oneline -10` to orient. Be quick but real — don't plan blind.
3. Produce a concrete plan:
   - Restate the goal and the **Definition of Done** (observable, testable).
   - An ordered **task breakdown** — each unit independently committable and verifiable.
   - The exact **build / test / lint** commands that will be the gate.
   - **Risks** and the **assumptions** you'll make if you hit ambiguity at 3am.
4. If there is a **real fork** (an approach/architecture choice with meaningful trade-offs),
   present 2–3 options with a recommendation and use `AskUserQuestion` so the user picks. If there
   is no real fork, present the single plan.
5. **STOP and wait for explicit GO.** Do not start Phase 2 until the user approves — a pick + "GO",
   or a plain "GO" / "yes" / "onayla". If the user amends the plan, fold it in and re-confirm.

---

## Phase 2 — Autonomous execution  (after GO: NO questions until done)

Once GO is given, run to completion without asking anything. Operating contract:

### Never stop to ask
If a decision comes up, choose the option most consistent with the approved plan, the frozen
decisions, and the existing code patterns. **Log the assumption** in the run report and keep
going. Stopping to ask is failure for this skill.

### Stay reversible — branch only
- Work on a dedicated branch: `git switch -c autopilot/<short-slug>` (or the branch the user named).
- **Never** push to `main`, merge into a shared branch, open a production PR, deploy, delete data,
  rewrite published history, or take any outward-facing / irreversible action. Those wait for morning.
- `bypassPermissions` means nothing will stop a dangerous command but you — so don't run one.

### Tests are the gate
After each unit: run build + tests + lint (per `CLAUDE.md` → "Project specifics"). If red, fix
before moving on. Never commit a knowingly-broken tree or call a unit "done" with failing checks.

### Commit incrementally
One coherent commit per completed unit, using the project's message convention. Progress must be
visible in `git log` and recoverable if the session ends mid-run.

### Bound the loops (anti-spin)
If the same failure recurs about 3 times with no progress, **stop attacking it**: log it as
blocked with the full error, skip to the next independent unit, and surface it in the morning
report. Do not burn the night on one wall.

### Keep a morning report
Maintain a run log at `docs/AUTOPILOT-<date>.md` (or append to STATE / CHANGELOG): each unit
done, every assumption made, anything blocked, final test status, and the branch name. End the
run with a summary message: what shipped, what's on the branch, what's blocked, and what the user
should review first.

### Scope discipline
Do only the approved scope (plus queued items if that was the agreed scope). No silent scope
creep. Adjacent work is allowed only when strictly required to make the approved scope pass its
tests — and it gets logged.

### Use the team if present
If the project has the orchestration kit's subagents (`planner` / `builder` / `reviewer`),
delegate to them: planner for any unit you didn't fully scope in Phase 1, builder to implement,
reviewer on the diff before the final commit of each unit. Otherwise do the work directly.

### Match power to the task (generous escalation)
Don't run every rung at the same power. Judge each unit's difficulty and match it — biasing UP when
unsure (generosity), down only when the work is genuinely trivial:
- **Trivial** (scaffold, rename, doc, one-liner): cheapest model (Haiku/Sonnet), low effort.
- **Normal rung** (a feature + its tests): Sonnet, default effort.
- **Hard** (tricky algorithm, correctness-critical logic, a bug that didn't fall on the first try,
  the review pass): Opus, and raise effort — escalate deep reasoning by running it as a **Workflow**
  whose hardest stages use Opus + `xhigh` effort (a plain subagent spawn can set `model` but not effort).
- **Generosity rule:** between two tiers, pick the higher. Under-powering a hard rung at 3am is the
  expensive mistake; a stubborn bug is a signal to escalate, not to grind on the cheap tier.

---

## Stop conditions
Finish when **all units are done with green checks**, OR the remaining units are **all blocked**.
Either way: write the morning report and the summary. Do not invent make-work to "use up" time —
a clean early finish is a success, not a waste.
