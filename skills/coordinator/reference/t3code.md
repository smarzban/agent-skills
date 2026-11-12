# In T3 Code

Marker: the environment variable `T3CODE_THREAD_ID` is set, and `delegate_task` is in your tool list. The tool names below are from T3 Code's own instructions to an agent on 2026-11-12; check your tool list before you rely on one. Those instructions decide how the tools are used. Where they differ from this file on how to do a step, follow them; they never widen an ending or the gate.

## Starting a builder

- A builder is a delegate with its own checkout. Call `orchestrator_capabilities` once, then start each builder with `delegate_task` and `isolation: "worktree"`. Never make a builder's checkout yourself in the shell, and never run a builder in your own checkout.
- The worktree starts from your current commit. The brief names the base branch; the builder branches from its tip as its first action.
- The task text is the whole of what the builder knows. Give it where the brief is (the task id, or the path and the heading), and "Follow the `deliver` skill." Put no secret in it.
- A new top-level thread is made only when the owner asks for one. "Builder", "delegate" and "parallel" do not mean one.

## Waiting

- Do not poll a running delegate: no `task_status` loops, no `sleep`, no watchers. Its completion wakes you. Follow the `next` field of the call that started it, which usually means ending your turn.
- A delegate that stalls never completes, so it never wakes you. While you are active, if one has run far longer than its task should, read its thread once with `t3_thread_read`; if it waits on a question, answer it. Once you have ended your turn, only the owner or another completion brings you back. Tell the owner this before they leave you in charge.
- A delegate cannot watch a pull request. Its last line reports the pull request URL, the head commit and the state of checks and reviews. If the checks were still running, you wait on them.

## Pull requests

- Link each pull request to the thread with `link_pull_request` (or `link_pull_requests` for several), straight after it is created or pushed to, including ones your builders opened.
- When you wait on checks or reviews yourself, call `watch_pull_request` and end your turn. No `gh --watch`, no loops. Call `unwatch_pull_request` before you hand back.

## Sending work back

A delegate that has handed back is finished. To have findings fixed, a failing check worked, or a branch brought up to date, start a new builder with `delegate_task` and a worktree: give it the pull request number, the list of what to do, and "Follow the `deliver` skill for an open pull request. Started by a coordinator: your ending is stop, and you run no review."

T3 Code may tell you to fix a small review finding yourself. A commit you write is yours: prove it again by the `deliver` skill's proof reference, and have the next review round cover it.

## Reviewing

- Each review is a new `delegate_task` with the review role and a worktree. Its first action is `git fetch` and `git checkout --detach <head sha>`. Give it the diff range, the contract and the areas to look at, and none of your reasoning. Do not send a second review to an earlier reviewer's thread.
- The independent check before a send is also a new delegate with a worktree, checked out at the head commit the same way.

## Asking the owner

Use the question tool, so that the thread shows it is waiting. For a login, a token or a password, use `request_secret`; never ask for one in chat.

## Cleaning up

Stop a delegate you no longer need with `task_cancel`. A delegate's worktree belongs to T3 Code: do not remove it in the shell; list it for the owner if it is still there. With the tsk board, `tsk clean` applies only to tasks that tsk dispatched.
