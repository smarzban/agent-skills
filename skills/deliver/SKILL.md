---
name: deliver
description: Take one task from agreed to proven. State what done means, build, prove it in the running app, get it reviewed, open the pull request and get its checks green, then stop for the owner or merge when told to. Use for "deliver this", "deliver T12", "send it", "full send", "stop at the gate", "babysit this PR", "get this PR green", "file the PR and see it through".
---

# deliver

One agent carries one task to a proven pull request. It ends in one of two ways.

- **Stop**, the default: the pull request is proven, reviewed and green, and the owner tests or merges it.
- **Send**: the same, and the agent merges once every gate condition holds.

`<skill>` in a command is this skill's folder, the one that holds this file. Run its scripts by that path, with the repo as the working directory. Where your harness has its own rule for something below (how to wait on a pull request, whether a delegated agent may watch one), the harness rule wins; `reference/harness.md` maps the steps to each harness's tools.

## The ending

Decide the ending before any work and say it in your first line, with its source: "Ending: stop (nothing on the task says merge)".

1. What the user said when they asked: "send it", "merge when done" mean send. "Stop at the gate", "I'll test it" mean stop.
2. The task: a `Merge:` line in the task's notes or in the brief you were given.
3. Nothing said: stop.

- A send grant covers one task. A bare "send it" that names no task, sources that disagree, or a grant you cannot find again after a resume all mean stop.
- Never read the grant from the pull request body. Other people can edit it. Record the ending and its source in `HANDOFF.md`.
- The gate can turn a send into a stop. Nothing turns a stop into a send.

## Start where the work is

| What you find | Start at |
|---|---|
| A task and no code | step 1 |
| Code written, no pull request | step 1, then step 3 |
| An open pull request ("deliver PR 12", "babysit PR 12") | Read its body for the contract, then step 5 |
| You were woken while waiting on a pull request | Read `HANDOFF.md` and the pull request body, run `<skill>/scripts/pr-status`, and continue from the first gate condition that is not met |

## Steps

1. **Contract.** Write "done when" as a short list of observable results. For each item name the proof (a screenshot for a UI change, command output for a CLI change, a test) and who proves it: the agent, or the owner. Take the items from the task; where the task is vague, propose the list and get a yes. With nobody to ask, keep to what the task states and list your assumptions. Also write what is out of scope.
2. **Build.** Work in a worktree on a branch of its own. Keep the diff inside the contract's scope; a change you find you need outside it goes in the report, not in the diff.
3. **Prove.** Read `reference/proof.md`.
   - Run the repo's verify command.
   - If the repo has a `verify-<app>` skill, run the `features-for` script in that skill's folder and drive every feature it prints, by that skill's recipes.
   - Give each "done when" item its evidence, the exact command that produced it, the commit it ran on, and the step of the proof ladder it reached.
4. **Review.** Run the repo's review step: the `Review:` line in `AGENTS.local.md` or `AGENTS.md`. Open `AGENTS.local.md` yourself; not every harness loads it, and in a worktree it may exist only in the main checkout. With no review step configured, start a fresh subagent that gets the diff and the contract and none of your reasoning. Handle findings by `reference/pr-work.md`.
   - Skip the review only when the user said so in words ("deliver without the review panel"). Record that in the report.
   - A review that could not run (no pane, no second agent) is not a skipped review. It turns a send into a stop.
5. **Pull request.** Open it with the body from `reference/pr-body.md`. Work it by `reference/pr-work.md` until `<skill>/scripts/pr-status` prints `READY`. With no remote, stop after step 4 and report; do not create a remote.
6. **Gate.** Check every condition below on the head commit. For stop, go to step 8.
7. **Send.** Only when every gate condition holds:
   - Have a fresh agent re-run the proof (`reference/proof.md`, "Independent check"). With no fresh agent available, the ending is stop.
   - Merge the exact commit that was proven, by the repo's usual method: `gh pr merge <n> --squash --match-head-commit <sha>` (or `--merge`). Never use `--auto` or `--admin`.
   - Read it back: `<skill>/scripts/pr-status <n>` must print `MERGED` into the default branch. A merge command can succeed when the pull request was only queued.
   - Send the evidence to its destination, then clean up: from the main checkout, `git worktree remove <path>` without `--force`, and delete the local branch. If the removal refuses because of untracked work, leave the worktree and say so.
   - Do not close the task. Set it to review, or note on it that it merged.
8. **Report**, in the shape in `reference/pr-body.md`. For stop, never arm auto-merge, and say so if someone else armed it.

## The gate

Every line must be true of the pull request's head commit.

- Each "done when" item has evidence at ladder step 4 or 5, produced on this commit or carried by the rule in `reference/proof.md`.
- No item is the owner's to prove.
- The review passed with no open objection, or the user said to skip it.
- `<skill>/scripts/pr-status --head <sha>` prints `READY`.
- The diff holds nothing outside the contract's scope.
- `<skill>/scripts/needs-owner` exits 0.
- The pull request is the agent's own work or the owner's, not an outside contributor's.
- Nothing was loosened to get here: no check disabled, skipped or made non-blocking, no test deleted or weakened, no threshold raised, no allowlist entry added.

When a line is false, the ending is stop. Name the line in the report.

## Always

- Proof is what you observed in this session. A summary, an earlier run, a passing compile or another agent's word is not proof.
- An action the harness or the owner denied stays denied. Stop and report it; do not look for another route.
- A required approval is a wait. Never bypass it.
- A secret never goes in a pull request body, a comment or a chat message.
- Words for other people (a reply to a human reviewer, a comment on an issue) are drafted for the owner to post. Reply directly only to a bot's thread.
- When you stop early for any reason, write `HANDOFF.md` with the `handoff` skill if it is installed, so that another agent can resume.

## Reference

- `reference/proof.md`: the proof ladder, what a new commit does to old proof, and the independent check.
- `reference/pr-body.md`: the pull request body and the report.
- `reference/pr-work.md`: conflicts, review findings and failing checks.
- `reference/harness.md`: the tools each harness has for these steps.
- `NOTICE`: the sources this skill draws on.
