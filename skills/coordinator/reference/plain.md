# In a plain terminal

None of the markers is set, or a marker is set and its probe failed.

## Starting a builder

- If your harness can start subagents, a builder can be one, provided it works in its own worktree.
- Otherwise make the worktree yourself and start an agent in it, in its own terminal tab or as a command-line run:

  ```sh
  git worktree add -b <branch> ../<repo>-<task> <base>
  ```

- Give it where the brief is and the path of the `deliver` skill to follow. With the tsk board, see `trackers.md` for starting a task outside Herdr and Rex.
- Confirm that the agent started working before you turn to the next task.

## Waiting

- With a tracker, the task's status is the signal.
- Without one, the signal is the builder's `HANDOFF.md` and its pull request: run the `handoff` skill's `handoff-status` script, and the `deliver` skill's `pr-status` on the branch.
- A subagent hands back when it ends; a command-line run ends when its process does. Do not assume either means the work is proven; read the report.

## Sending work back

A subagent or a command-line run that has handed back is finished. For findings to fix, a failing check, or a branch to bring up to date, start a new one in the same worktree. Give it: the pull request number and branch, the head commit you expect, the list of what to do, the task's "done when" list, the owner's limits, and "Follow the `deliver` skill at <path> for an open pull request. Started by a coordinator: your ending is stop, and you run no review. Hand back the new head commit and the proof you ran again."

## Reviewing

Start the reviewer as a fresh subagent, or as a command-line run of another agent in a clean checkout of the head commit. It gets the diff range, the contract and the areas to look at.

## Cleaning up

Once the pull request reads as merged, by you or by the owner: close the builder's tab or let its process end, then `git worktree remove <worktree>` and `git branch -D <branch>`.
