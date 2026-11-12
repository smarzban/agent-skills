# The review

You run the review on each builder's pull request. The builder's brief told it not to.

## Running it

- Use the repo's review step: the `Review:` line in `AGENTS.local.md`, or in `AGENTS.md` as it is on the base branch. With none, start a fresh agent that gets the diff and the contract and none of your reasoning, as `deliver` step 4 describes.
- The reviewer works in its own fresh session. Never review a builder's work yourself when a reviewer can be started. Your environment's reference says where to open it.
- Give it the pull request's base and head commits and the "done when" list. Name the areas that matter for this change: security boundaries, writes that other people see, compatibility with what is deployed.
- Review one pull request at a time, unless you have checked that your review step can run two sessions side by side without mixing them up.
- A review that cannot run is not a review that passed. The ending of that task is stop; say so.

## Deciding what to keep

Check each finding against the code. Keep or set aside by how likely it is and how much it would cost, and write one line of reason for each.

| Keep | Set aside |
|---|---|
| Security, a leak of credentials or data | A rare race with low impact or a workaround |
| A wrong or duplicate write that other people see | A request for more test coverage alone |
| A cost or denial-of-service hole, data loss | A path this deployment does not use |
| Breakage on the main path, and anything cheap to fix | Style that the repo's rules do not back |

- Send the kept findings to the builder to fix, and pass on the list you set aside so it knows what was seen.
- The builder fixes with new commits. Proof for the changed code runs again, by the "new commit and old proof" table in the `deliver` skill's proof reference.
- The reviewer then checks the kept findings on the new head and looks for regressions.
- At most three passes: the first review, then up to two rounds of fix and check. After that, list what is still open for the owner; do not keep going.
- A branch that is behind a pull request merged since its review is brought up to date before its review counts.

## What the owner sees

- Every finding set aside goes in the pull request body under **Review**, with its reason.
- Never resolve a review thread that a person opened.
- A close-out comment, or any other words posted where other people read them, is drafted for the owner. Post it only when the owner says so, and never while they are away.
