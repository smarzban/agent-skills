# In Rex

Marker: the environment variable `REX_SESSION` is set. The `rex` CLI owns the commands: run `rex __internal skill` for its guide, or `rex whoami --json` and `rex --help` on an older version. The `rex window new` and `rex split` lines below were read from the coordinator skill that ships with tsk, version 1.7.0, on 2026-11-12, and were not run here; confirm them against the Rex guide.

## Starting a builder

Keep builders in your own session, as tabs. Never open a new session for one.

- With the tsk board: set the task's assignee and base, then `tsk dispatch T12` from your own pane. The tab, named `t12-<assignee>`, opens in the session tsk runs in.
- Without tsk, or to open the tab yourself, make the worktree and then:

  ```sh
  rex window new -s "$REX_SESSION" t12-builder --focus=false -c <worktree> -- <agent> "<prompt>"
  ```

- The prompt only points at the brief: with tsk, "T12: read the brief with `tsk list T12 --json`, then follow the deliver skill." Without it, the path and the heading of the brief.
- After starting it, read the tab's output to confirm the agent is working.

## Waiting

- With tsk, the signal is the task's status: `review` waiting on you, or `blocked`. Read it with `tsk list T12 --json`.
- Without tsk, have the builder message you when it is done, or read its tab.
- To judge a quiet builder, look at its branch, its pushes and its `HANDOFF.md`, then read its tab.

## Sending work back

For findings to fix, a failing check, or a branch to bring up to date: with tsk, `tsk reply T12 "…" --send` reaches a dispatched builder. Otherwise type the message into the builder's tab, by the Rex guide. If its session has ended, open a new tab in the same worktree, with the pull request number and the list.

## Reviewing

Make a clean checkout of the head commit (`git worktree add --detach ../<repo>-review <sha>`, removed afterwards) and open the reviewer as a pane in the builder's tab, without taking focus:

```sh
rex split -s "$REX_SESSION" -b <builder block> --focus=false -c <review checkout> -- <reviewer>
```

If the repo's review step is a skill of its own, follow it for the prompts.

## Cleaning up

For a dispatched task, `tsk clean T12` closes the tab and removes the worktree and the branch. For a builder you started yourself, close its tab, then `git worktree remove <worktree>` and `git branch -D <branch>`. Never close a tab you did not open.
