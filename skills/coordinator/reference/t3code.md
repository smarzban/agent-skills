# In T3 Code

Marker: the environment variable `T3CODE_THREAD_ID` is set (seen on 2026-11-12). T3 Code's own instructions to you decide how its tools are used; where they differ from this file, follow them.

## Starting a builder

- A builder is a delegate. Call `orchestrator_capabilities` once, then start each builder with `delegate_task`, with `isolation: "worktree"` so that it gets its own checkout. Never make a builder's checkout yourself in the shell.
- The task text is the whole of what the builder knows. Give it where the brief is (the task id, or the path), and "Follow the `deliver` skill." Put no secret in it.
- A builder on your own provider and model, with no settings beyond yours, can be a native subagent instead. Use `delegate_task` for another provider's model, or when the builder must run in the background.
- A new top-level thread is made only when the owner asks for one. "Builder", "delegate" and "parallel" do not mean one.

## Waiting

- Do not poll a running delegate: no `task_status` loops, no `sleep`, no watchers. Its completion wakes you. Follow the `next` field of the call that started it, which usually means ending your turn.
- That makes a stall hard to see from inside. When a delegate has run far longer than its task should, read its thread once with `t3_thread_read`; if it is waiting on a question, answer it.
- A delegate cannot watch a pull request. Its last line reports the pull request URL, the head commit and the state of checks and reviews, and you carry on from there.

## Pull requests

- Link each pull request to the thread with `link_pull_request` (or `link_pull_requests` for several), straight after it is created or pushed to, including ones your builders opened.
- When you wait on checks or reviews yourself, call `watch_pull_request` and end your turn. No `gh --watch`, no loops. Call `unwatch_pull_request` before you hand back.

## Reviewing

- Each review is a new `delegate_task` with the review role, given the diff range, the contract and the areas to look at, and none of your reasoning. Do not send a second review to an earlier reviewer's thread.
- Fix a small finding yourself only when you are the builder. As coordinator, send kept findings to the builder.
- The independent check before a send is also a new delegate, in its own worktree at the head commit.

## Asking the owner

Use the question tool, so that the thread shows it is waiting. For a login, a token or a password, use `request_secret`; never ask for one in chat.

## Cleaning up

Stop a delegate you no longer need with `task_cancel`. Worktrees made for delegates are T3 Code's; remove one only through its tools or when the owner asks. With the tsk board, `tsk clean` applies only to tasks that tsk dispatched.
