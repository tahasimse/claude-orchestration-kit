# 0006 — Match model power to the unit of work; escalate by delegating

- **Status:** accepted
- **Date:** 2026-06-23 (recorded retroactively 2026-07-29)

## Context
Two failure modes bracket this choice. Run everything on the cheapest model and hard reasoning —
architecture, a bug that did not fall on the first try, a review — comes back confidently wrong.
Run everything at maximum model and effort and the cost is absurd for work that is mostly
mechanical, while the supervisor session burns its own budget on turns that needed none of it.

The asymmetry that resolves it: the roles that reason hardest are the **short** ones. A planner
produces a plan; a reviewer produces findings. The role that runs longest — the builder, grinding
through files and test loops — is the one doing the least novel reasoning per token.

## Decision
Route power per unit of work rather than per session. `planner` and `reviewer` default to `opus`;
`builder` defaults to `sonnet`. The human keeps a calm baseline (Opus, medium effort) and the
Orchestrator **escalates by delegating**, not by maxing out its own turn: trivial mechanical work
goes to the cheapest subagent at low effort, normal implementation to a Sonnet worker, hard
reasoning to Opus at high or xhigh effort, and genuinely broad or verification-heavy work to a
Workflow that can fan out and set `model` and `effort` per stage. Agent-file `model:` values are
defaults, overridable per spawn. **Generosity rule: between two tiers, pick the higher** — the sin
is under-powering a hard task, not over-spending on an easy one.

## Consequences
- Cost tracks difficulty instead of session length, and the expensive model is spent where a wrong
  answer is expensive.
- The Orchestrator stays cheap per turn even while commanding expensive work.
- The plain Agent tool cannot raise a single subagent's *effort*, only its model. Deep reasoning
  therefore has to be escalated through a Workflow or a higher-effort human session — a real
  limitation, and the reason Workflows are in the policy at all.
- "Pick the higher tier when unsure" will sometimes overspend. Accepted deliberately, because the
  opposite error is silent.
- Model names and tiers will drift as models are released. This ADR fixes the *rule*
  (power ∝ difficulty; escalate by delegating), not the specific model names.

## Alternatives considered
- **One model for everything** — rejected: either under-powers reviews or overpays for the build
  loop; there is no single setting that is right for both.
- **Max model and effort everywhere** — rejected: the builder is the token-heaviest role and the
  least reasoning-dense, so this pays the most exactly where it buys the least.
- **Let each session decide ad hoc** — rejected: routing becomes a coin flip, and the failure
  (under-powering) is invisible until the output is wrong.
