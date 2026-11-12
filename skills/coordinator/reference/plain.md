# In a plain terminal

None of the markers is set: no `T3CODE_THREAD_ID`, `HERDR_ENV` is not `1`, and no `REX_SESSION`.

## Starting a builder

- If your harness can start subagents, a builder can be one, provided it works in its own worktree.
- Otherwise make the worktree yourself and start an agent in it, in its own terminal tab or as a command-line run:

  ```sh
  git worktree add -b <branch> ../<repo>-<task> <base>
  ```

- Give it where the brief is and "Follow the `deliver` skill." With the tsk board outside Herdr and Rex, `tsk dispatch` answers `no-host`: set the task started with `tsk status T12 started --no-dispatch` and start the agent yourself.
- Confirm that the agent started working before you turn to the next task.

## Waiting

- With a tracker, the task's status is the signal.
- Without one, the signal is the builder's `HANDOFF.md` and its pull request: run the `handoff` skill's `handoff-status` script, and the `deliver` skill's `pr-status` on the branch.
- A subagent hands back when it ends; a command-line run ends when its process does. Do not assume either means the work is proven; read the report.

## Reviewing

Start the reviewer as a fresh subagent, or as a command-line run of another agent in a clean checkout of the head commit. It gets the diff range, the contract and the areas to look at.

## Cleaning up

Close the builder's tab or let its process end, then `git worktree remove <worktree>` and `git branch -D <branch>`, once its pull request reads as merged.
