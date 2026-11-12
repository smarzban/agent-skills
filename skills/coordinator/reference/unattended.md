# When the owner leaves you in charge

## Before they go

Walk every task through build, proof, review, merge and deploy in your head, and ask for everything that would stop you, in one batch.

| Ask for | Examples |
|---|---|
| The ending of each task | Which tasks to send, which to stop. Record each in the ledger in their words |
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

A task whose paths are on the repo's **Needs the owner** list will stop however it was granted. Tell them which tasks those are before they go.

## While they are away

- Blocked on the owner: block the task with the exact question, and move to the next.
- These wait for the owner, whatever was granted: anything that cannot be undone, anything other people see (a comment that closes out a pull request or an issue, a release note, a message), production data, a go-live, an admin command, and anything outside the grants.
- A task without a ledger entry ends in stop.
- Keep `HANDOFF.md` current after every state change. If you are replaced or woken with no memory, it is all the next agent has.
- Stop when the limit they gave is reached, and write down where things stand.

## When they return

Report in this order:

1. What merged, with the proof and the review outcome for each.
2. What is ready and waiting for them, with the pull request and their exact steps.
3. What is blocked, and on which answer.
4. What you set aside or did not do, and why.
5. Last, the commands or clicks they need, ready to copy.
