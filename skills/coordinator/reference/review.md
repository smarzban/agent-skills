# The review

You run the review on each builder's pull request. The builder's brief told it not to.

## Running it

- Use the repo's review step: the `Review:` line in `AGENTS.local.md`, or in `AGENTS.md` as it is on the base branch. With none, start a fresh agent that gets the diff and the contract and none of your reasoning, as `deliver` step 4 describes.
- The reviewer works in its own fresh session, in a clean checkout of the head commit, not in the builder's worktree. Your environment's reference says where to open it.
- Give it the pull request's base and head commits and the "done when" list. Name the areas that matter for this change: security boundaries, writes that other people see, compatibility with what is deployed.
- Review one pull request at a time, unless you have checked that your review step can run two sessions side by side without mixing them up.
- With no reviewer, the ending of that task is stop. Your own reading of the diff is not the review.

## Findings

Check each finding against the code. For how to act on them, and how many rounds to run, follow `pr-work.md` in the `deliver` skill's `reference` folder. Where the repo's review step defines its own way to re-check fixes (a panel that verifies the kept findings in the same session), use that for the re-check.

Decide what to send back by how likely a finding is and how much it would cost:

| Send back to be fixed | May be set aside |
|---|---|
| Security, a leak of credentials or data | A rare race with low impact or a workaround |
| A wrong or duplicate write that other people see | A request for more test coverage alone |
| A cost or denial-of-service hole, data loss | A path this deployment does not use |
| Breakage on the main path, and anything cheap to fix | Style that the repo's rules do not back |

- Setting a finding aside is allowed for a stop: the owner sees it and decides. A real finding you set aside turns a send into a stop.
- Send the kept findings to the builder by your environment's reference, with the list you set aside, so that it knows what was seen.
- A fix is a new commit. Proof for the changed code runs again, by the "new commit and old proof" table in the `deliver` skill's proof reference.

## What the owner sees

- You write the outcome into the pull request body under **Review**: who reviewed, on which commit, the result, and each finding set aside with its reason.
- Never resolve a review thread that a person opened, and check that the builder did not.
- A close-out comment, or any other words posted where other people read them, is drafted for the owner. Post it only when the owner says so, and never while they are away.
