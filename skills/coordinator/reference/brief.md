# The brief

The brief is everything the builder knows. It goes into the task, in the place `trackers.md` names. Before you write into a field the owner wrote, ask whether to add to it or replace it.

```markdown
## Goal
<What this task achieves, in one or two sentences.>

## Context
- <A fact you checked, with `file:line` for every claim about existing code.>

## Decisions
1. <The design, as numbered decisions already made. The builder does not reopen them.>

## Done when
| # | Result | Proof | Proved by |
|---|---|---|---|
| 1 | <observable result> | <screenshot, command output, test> | builder |
| 2 | <observable result> | <the exact steps> | owner |

## Out of scope
- <What this task does not touch, and who owns it if another builder does.>

## How to work
Follow the `deliver` skill, with these settings:
- Ending: stop. Open the pull request, get its checks green, then hand back with the report. Do not merge.
- Review: coordinator. Do not run a review; the coordinator runs it on your pull request.
- Do not write or change a `Merge:` line on this task.
- Base branch: <branch>.
- Hand back by: <the completion signal from the environment reference>.
```

- **Done when** is the contract. Choose the proof from the task: a screenshot or recording for something a person looks at, output and exit code for a CLI, a response for an API, a test for logic with no surface. Name the owner as the prover only for what an agent cannot do.
- The builder's ending is always stop, whatever the task's ending is. A send is yours to carry out after review, so the brief never passes a merge grant on.
- Leave out what `deliver` and the repo's `AGENTS.md` already say: reading the project rules, running the verify command, not printing secrets.
- A builder that finds the brief wrong stops and says so. It does not quietly build something else.
