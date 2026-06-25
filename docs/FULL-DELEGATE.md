# Full-delegate variant

This branch makes the **main session a pure supervisor**: it delegates *everything* — including
planning — and **never reads or edits source code itself**. It frames the work, picks who does it,
reads the workers' reports, gates with you, and keeps the state docs honest. Nothing else.

Two things on this branch activate it:
- `.claude/agents/orchestrator.md` — the supervisor system prompt.
- `"agent": "orchestrator"` in `.claude/settings.json` — applies that profile to the main session.

## How it differs from `main`

| | `main` (lean orchestrator) | `full-delegate` (pure supervisor) |
|---|---|---|
| Reads source files? | Yes — STATE + decisions + the in-scope code | **No** — only STATE, decisions, and worker reports |
| Writes the plan? | Can, for small tasks (skips `planner`) | **Never** — always delegates to `planner` |
| Context size over time | small | **smallest** — only reports accumulate |
| Judges a result by | some direct grounding + the reviewer | reports + the reviewer's *executed* verdict |

## When this wins
- **Large, long-horizon, parallelizable work.** Keeping the supervisor's context to just reports is
  the cheapest way to run for hours without context bloat — the cost lever that dominates long sessions
  (re-sent context, even cached, is the thing that grows).
- **Many independent units.** A pure dispatcher fanning out to fresh workers shines when the pieces
  don't depend on each other.
- **A clean audit trail.** Every decision reads as "I read report X → I dispatched Y." Legible.

## When `main` wins — the honest caveats
- **Small or tightly-coupled work.** Always delegating planning is pure overhead on a one-file fix,
  and a parser that grows rung by rung wants *one* author, not a fresh builder per commit (the kit's
  own field test found this). A supervisor that never reads code can't notice that drift directly.
- **Judging plan quality.** With zero file grounding, the supervisor trusts the planner's report. If
  the planner misreads the codebase, the supervisor has no independent way to catch it before Gate 1 —
  it leans entirely on making workers show evidence. That's more round-trips and more latency.

**Rule of thumb:** `full-delegate` for big / parallel / long-horizon; `main` for small / coupled / interactive.

## Test it
1. Pull this branch and copy it into a throwaway project (see the README "Get started").
2. Run a real task and watch **who does the work** — the supervisor should never open a source file;
   the plan, the edits, and the *executed* review all come from subagents.
3. Run the same task on `main` and compare: token cost, latency, and whether result quality holds.

## Troubleshooting
- If the main session **can't launch subagents** in your Claude Code build, remove
  `"agent": "orchestrator"` from `.claude/settings.json` — `CLAUDE.md` alone still gives the soft
  supervisor behavior (enforced by instruction rather than by the agent profile).
- Want a **harder** lockdown — make it physically unable to touch code? Add a `tools:` line to
  `orchestrator.md` that omits `Grep` / `Glob` / `Edit` / `Write` over source, but keeps the
  subagent-launch tool plus `Read` / `Write` for `docs/`. Start soft; tighten only if it still reaches
  for the code.
