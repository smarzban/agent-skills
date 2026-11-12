# The brief

The brief is everything the builder knows. It goes into the task, in the place `trackers.md` names. Before you write into a field the owner wrote, ask whether to add to it or replace it.

```markdown
## Goal
<What this task achieves, in one or two sentences.>

## Context
- <A fact you checked, with `file:line` for every claim about existing code.>

## Decisions
1. <The design, as numbered decisions already made. Do not reopen them.>

## Done when
| # | Result | Proved by | Proof |
|---|---|---|---|
| 1 | <observable result> | agent | <screenshot, command output, test> |
| 2 | <observable result> | owner | <the exact steps> |

## Out of scope
- <What this task does not touch, and who owns it if another builder does.>

## How to work
Follow the `deliver` skill, with these settings:
- Started by: a coordinator. Your ending is stop: open the pull request, get its checks green, then hand back with the report. Never merge.
- Do not run a review. The coordinator reviews your pull request.
- Branch from <base branch>, at its current tip.
- Never deploy, and never restart a service that others use.
- If this brief is wrong, stop and say so. Do not build something else.
- Hand back by: <the completion signal from the environment reference>.
```

- **Done when** is the contract. Name the owner as the prover only for what an agent cannot do.
- The brief never carries a merge grant, and the builder's ending is stop whatever the task's ending is. A send is yours to carry out after review.
- Leave out what `deliver` and the repo's `AGENTS.md` already say.
