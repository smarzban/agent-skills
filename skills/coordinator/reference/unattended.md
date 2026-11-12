# When the owner leaves you in charge

The owner has left you in charge when they say they will be away, or that you should carry on without asking. Until then, ask.

## Before they go

Walk every task through build, proof, review and merge in your head, and ask for everything that would stop you, in one batch.

| Ask for | Examples |
|---|---|
| Which tasks to send | Show each task's "done when" first. Record each yes under **Sends**, in their words |
| What a merge sets off | Whether a merge to this branch deploys or releases anything. If it does, it is a go-live: ask for it by name, or those tasks stop |
| Access | Logins, tokens, signing, cloud or CI access, test accounts, devices |
| Settings only they can change | Dashboards, DNS, anything that needs admin rights |
| Decisions | Open design questions, naming, scope cuts, forks you can foresee |
| Limits | Cost or quota caps, rate limits, how long to keep going |
| Test environments | A staging backend, a simulator, a sandbox repo, seeded data |

Prove each thing with one cheap probe while they are still there:

- each access works;
- a builder starts and hands back in this environment;
- the review step is reachable and returns a result;
- a fresh agent can be started for the independent check, if any task is to be sent.

Tell them before they go:

- which tasks will stop whatever was granted, because they touch a **Needs the owner** path;
- that if your context is summarised and their words are lost, or another agent takes over, every send that is not a `Merge: send` line on the task becomes a stop;
- in T3 Code, that a builder that stalls after you end your turn will not wake you.

## While they are away

- Blocked on the owner: block the task with the exact question, and move to the next.
- These wait for the owner, whatever was granted: anything other people see (a comment that closes out a pull request or an issue, a release note, a message, a picture sent to the `Evidence:` destination), production data, a go-live they did not name, an admin command, anything outside the grants, and anything that cannot be undone other than the merge of a task they said to send.
- After a send merges, keep its evidence files and its worktree until they are back.
- Keep `HANDOFF.md` current after every state change. If you are replaced or woken with no memory, it is all the next agent has.
- Stop when the limit they gave is reached, and write down where things stand.

## When they return

Report in this order:

1. What merged, with the proof and the review outcome for each.
2. What is ready and waiting for them, with the pull request and their exact steps.
3. What is blocked, and on which answer.
4. What you set aside or did not do, and why.
5. Last, the commands or clicks they need, ready to copy.
