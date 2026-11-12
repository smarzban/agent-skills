# In Herdr

Marker: the environment variable `HERDR_ENV` is `1`. The `herdr` skill owns the commands; read it, or run `herdr agent`, `herdr pane` and `herdr workspace` for the current options. Never run bare `herdr`: it opens the interface. The Herdr commands below were read from that skill and checked against `herdr`'s help on 2026-11-12; the tsk commands are from tsk 0.13.0.

## Starting a builder

- With the tsk board: set the task's assignee and base, then `tsk dispatch T12`. Dispatch makes the worktree, a Herdr workspace named `t12-<assignee>`, and the agent. If tsk's `config.toml` has no agent profile, ask the owner to add one, or start the builder yourself.
- Without tsk: make the worktree (`git worktree add -b <branch> ../<repo>-<task> <base>`), open a pane in it without taking focus, and start the agent there.

  ```sh
  herdr pane split --current --direction right --cwd <worktree> --no-focus
  herdr agent start t12-builder --kind <agent> --pane <pane-id>
  herdr agent prompt t12-builder "Read your brief at <path and heading>, then follow the deliver skill."
  ```

- After prompting, read the pane to confirm the agent is working: `herdr agent read t12-builder --source recent-unwrapped --lines 40`. A dialog can swallow a prompt.

## Waiting

- With tsk, the signal is the task's status: `review` waiting on you, or `blocked`. Read it with `tsk list T12 --json`.
- Without tsk: `herdr agent wait t12-builder --timeout <ms>`, which returns when the agent is idle, done or blocked. Then `herdr agent get` and `herdr agent read`.
- To judge a quiet builder, look at its branch (`git -C <worktree> log --oneline -5`), its pushes and its `HANDOFF.md`, then read its pane.

## Sending work back

For findings to fix, a failing check, or a branch to bring up to date: with tsk, `tsk reply T12 "…" --send` reaches a dispatched builder. Otherwise prompt the builder's agent again: `herdr agent prompt t12-builder "…"`. If its session has ended, start a new builder in the same worktree, with the pull request number and the list.

## Reviewing

Make a clean checkout of the head commit (`git worktree add --detach ../<repo>-review <sha>`, removed afterwards) and open the reviewer as a sibling pane in it, without taking focus. If the repo's review step is a skill of its own (for example a review panel run by another agent), follow that skill for the pane, the agent name and the prompts; it may reuse one named agent, in which case review one pull request at a time.

## Cleaning up

For a dispatched task, `tsk clean T12` closes the workspace and removes the worktree and the branch; it keeps a branch it cannot confirm merged, and says why. For a builder you started yourself, close its pane or agent, then `git worktree remove <worktree>` and `git branch -D <branch>`. Never close a pane or an agent you did not start.
