# Working a pull request to ready

Run `<skill>/scripts/pr-status <n>`. It prints one verdict and exits; it does not wait. Without a number it uses the current branch's pull request, so run it from the worktree. Act on the verdict, then wait for the next state the way your harness provides (`harness.md`).

Work in this order: conflicts, then review findings, then failing checks. Collect every fix you know of into one push, because each push restarts the checks.

## What to do for each verdict

| Verdict | Do |
|---|---|
| `READY` | Go to the gate. |
| `DRAFT` | When the proof and the review are done, mark it ready: `gh pr ready <n>`. |
| `CONFLICT` | See **Conflicts**. |
| `CHANGES-REQUESTED`, `THREADS-OPEN` | See **Review findings**. |
| `THREADS-UNKNOWN` | The threads could not be read. Read them on the host yourself. The ending is stop. |
| `CHECKS-FAILING` | See **Failing checks**. |
| `CHECKS-PENDING`, `COMPUTING` | Wait, then run `pr-status` again. |
| `NO-CHECKS` | If the repo has CI, the run has not started or was not triggered: wait and run it again, and after a few minutes find out why. If the repo has no CI, the verify command you ran is the only check, and the ending is stop. |
| `REVIEW-REQUIRED` | An approval from a person is missing. Report it and wait. This is not something to fix. |
| `BLOCKED` | A branch behind its base is brought up to date. Anything else is reported. |
| `HEAD-MOVED` | Someone pushed. Read the new commits before you do anything else. |
| `CLOSED` | Stop and report. Do not reopen it. |
| `MERGED` | Expected only after your own merge. If someone else merged, report it and stop. |

When you wake up after a wait, read the pull request and its threads again. The state you remember may be old.

## Conflicts

Bring the branch up to date with its base and resolve. Use the repo's stated method (`AGENTS.md`); where it states none, merge the base into the branch, which keeps reviewed commits intact. Never force-push a branch that someone else has pushed to. After a conflict you resolved, the affected proof runs again (`proof.md`).

## Review findings

They come from the repo's review step, from review bots and from people.

- Check each finding against the code before you act on it. A finding is real when you can show the failing case.
- A real finding in this diff: write the test or the recipe step that fails because of it, fix it, and see that it passes.
- A real finding about code the diff did not touch: note it for the report as a follow-up. Do not widen the diff.
- A finding you cannot show to be real: say why in one or two lines, and list it in the pull request body under **Review**. Opinions about style that the repo's rules do not back go the same way.
- Block only on a problem you can show: a bug a user would hit, a failed check, a doc line the change made wrong.
- Each round uses a fresh reviewer on the whole diff. A review counts from the first one you run, before or after the pull request exists. An objection that you fixed, followed by a round that passes, is a pass. A fix that changes only wording or the verification map needs no further round.
- Stop after three rounds. If findings keep coming, report what is still open and let the owner decide.

Threads and replies:

- A bot's thread: reply with the commit that fixed it or the reason it does not apply, and resolve it.
- A person's thread: write the reply and give it to the owner to post, unless the owner told you to post replies yourself. Never resolve a person's thread. The person or the owner does that, and until then the ending is stop.

## Failing checks

Read the log of each failing check before you do anything.

| What the log shows | Do |
|---|---|
| A failure in code this diff changed | Fix it in a commit. |
| A failure in code this diff never touched | The base is stale or already red. Check whether the base branch fails the same way; if the branch is behind, bring it up to date. Do not edit unrelated code to get green. |
| A timeout, a network error, a runner fault | Start the run again once. If it fails the same way, it is not a flake: read it again as one of the rows above. |
| A check that finished as stale or cancelled | Start it again once. |

Never make a check pass by loosening it, in any of the ways the gate lists (`SKILL.md`, "Nothing was loosened"). If a check is wrong, say so in the report and stop; changing it is the owner's decision.
