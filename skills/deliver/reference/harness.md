# What each harness provides

`deliver` names each step by what it needs. Use your harness's own tool for it where one exists. Where the harness's instructions and this skill differ on how to do a step, follow the harness.

| Need | T3 Code | Claude Code | Codex, Pi, others |
|---|---|---|---|
| Record the pull request against the work | `link_pull_request`, right after you create or push to it | nothing needed | nothing needed |
| Wait for checks and reviews | `watch_pull_request`, then end the turn; you are woken when the state changes. No `sleep`, loops or `gh --watch` | run `gh pr checks <n> --watch` as a background command | `gh pr checks <n> --watch` |
| Ask the owner something and show that you are waiting | the question tool | the question tool | a plain message, and set the task to blocked |
| Get a secret without putting it in the chat | `request_secret` | ask the owner to set it in the environment | ask the owner to set it in the environment |
| A fresh agent for review or the independent check | a subagent | a subagent | a second session in a clean checkout, or another harness's command-line mode |

The T3 Code tool names are from that harness's own instructions on 2026-11-12. The other columns are general practice, not probed per version. Check your harness's tool list before you rely on a name.

## Being woken

A harness that ends your turn while you wait may wake you with your earlier context summarised. Everything `deliver` needs to continue is kept outside your memory for that reason:

- the contract, the evidence and its commit: the pull request body;
- the ending and where it came from, and the next action: `HANDOFF.md`;
- the current state: `<skill>/scripts/pr-status`.

Read all three before you act.

## A delegated agent

An agent that another agent started may not be allowed to wait on a pull request. In that case it does not watch. It finishes what it can, and its last line reports the pull request URL, the head commit and the state of checks and reviews, so that its parent can carry on.
