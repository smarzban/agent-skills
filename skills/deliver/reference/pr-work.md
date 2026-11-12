# Working a pull request to ready

Run `<skill>/scripts/pr-status`. It prints one verdict and exits; it does not wait. Act on the verdict, then wait for the next state the way your harness provides (`harness.md`).

Work in this order: conflicts, then review findings, then failing checks. Collect every fix you know of into one push, because each push restarts the checks.

## Conflicts

`CONFLICT`: bring the branch up to date with its base and resolve. Use the repo's stated method (`AGENTS.md`); where it states none, merge the base into the branch, which keeps reviewed commits intact. Never force-push a branch that someone else has pushed to. After a conflict you resolved, the affected proof runs again (`proof.md`).

## Review findings

They come from the repo's review step, from review bots and from people.

- Check each finding against the code before you act on it. A finding is real when you can show the failing case.
- A real finding in this diff: write the test or the recipe step that fails because of it, fix it, and see that it passes.
- A real finding about code the diff did not touch: note it for the report as a follow-up. Do not widen the diff.
- A finding you cannot show to be real: say why in one or two lines and move on. Opinions about style that the repo's rules do not back go the same way.
- Block only on a problem you can show: a bug a user would hit, a failed check, a doc line the change made wrong.
- Stop after three rounds of review on the same pull request. If findings keep coming, report what is still open and let the owner decide.

Replies: answer a bot's thread directly, citing the commit that fixed it or the reason it does not apply. For a person's comment, write the reply and give it to the owner to post, unless the owner told you to post replies yourself.

## Failing checks

`CHECKS-FAILING`: read the log of each failing check before you do anything.

| What the log shows | Do |
|---|---|
| A failure in code this diff changed | Fix it in a commit. |
| A failure in code this diff never touched | The base is stale or already red. Check whether the base branch fails the same way; if the branch is behind, bring it up to date. Do not edit unrelated code to get green. |
| A timeout, a network error, a runner fault | Start the run again once. If it fails the same way, it is not a flake: read it again as one of the rows above. |

Never make a check pass by loosening it: no skipped or deleted test, no disabled job, no raised threshold, no new allowlist entry. If a check is wrong, say so in the report and stop; changing it is the owner's decision.

## Waiting

- `CHECKS-PENDING`: wait, then run `pr-status` again.
- `REVIEW-REQUIRED`: an approval from a person is missing. Report it and wait. This is not something to fix.
- `BLOCKED`: read the state it prints. A branch behind its base is brought up to date; anything else is reported.
- `HEAD-MOVED`: someone pushed. Read the new commits before you do anything else.

When you wake up after a wait, read the pull request and its threads again. The state you remember may be old.
