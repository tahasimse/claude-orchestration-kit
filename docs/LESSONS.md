# LESSONS

> Why a rule exists. When something goes wrong and a rule, config or doc changes as a result,
> the change goes where agents read it — and the *reason* goes here.
>
> **A lesson is recorded only when both are true:** something went wrong (or the human corrected
> the agent), **and** something changed because of it. No change, no lesson — that is a note, and
> notes do not belong in a file that lives forever.
>
> **Not read at session start.** Append-only, like `CHANGELOG.md`; grep it when you need a fact.
> **One exception:** before changing a rule — editing the gotchas in `CLAUDE.md`, the PLAYBOOK, or
> an agent file — read this first. Otherwise you will quietly undo a rule that exists for a reason
> you never saw.
>
> Rules get pruned when the problem goes away. Entries here do not: mark them `retired` instead, so
> the trail survives the rule.
>
> Where a rule belongs, and what earns a place in gotchas: `PLAYBOOK.md` → "Gotchas and lessons".

<!-- New entries go directly below this line, newest on top. -->

## <date> — <the lesson, in one line>
- **Source:** <what surfaced it — a run, a commit, a review, or "the human said so">
- **Result:** <what changed, by file and rule — or "none yet", which means this is not a lesson yet>
- **Status:** in force
