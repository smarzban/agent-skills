# The review

You run the review on each builder's pull request. The builder's brief told it not to.

## Running it

- Use the repo's review step: the `Review:` line in `AGENTS.local.md`, or in `AGENTS.md` as it is on the base branch. With none, use what the owner answered when you asked (see Settings in the skill). With nobody to ask, start a fresh agent that gets the diff and the contract and none of your reasoning, as `deliver` step 4 describes.
- The reviewer works in its own fresh session, in a clean checkout of the head commit, not in the builder's worktree. Your environment's reference says where to open it.
- Give it the pull request's base and head commits, the "done when" list, the owner's limits, and the repo's rule for running the app without touching real data. Name the areas that matter for this change: security boundaries, writes that other people see, compatibility with what is deployed. Ask for findings with a file and line and a failing case, and separately for remarks. It edits nothing and posts nothing.
- Fresh-agent reviews can run side by side. A review step that uses one named session reviews one pull request at a time.
- With no reviewer, the ending of that task is stop. Your own reading of the diff is not the review.
- A pull request that will have to take in another one first (same file, later in the merge order) is reviewed after it is brought up to date.

## Findings

Check each finding against the code. For how to act on them, and how many rounds to run, follow `pr-work.md` in the `deliver` skill's `reference` folder. Where the repo's review step defines its own way to re-check fixes (a panel that verifies the kept findings in the same session), use that for the re-check.

Decide what to send back by how likely a finding is and how much it would cost:

| Send back to be fixed | May be set aside |
|---|---|
| Security, a leak of credentials or data | A rare race with low impact or a workaround |
| A wrong or duplicate write that other people see | A request for more test coverage alone |
| A cost or denial-of-service hole, data loss | A path this deployment does not use |
| Breakage on the main path, and anything cheap to fix | Style that the repo's rules do not back |

- A finding is real when it shows a case that fails, or a "done when" item that does not hold. A remark shows neither; list remarks in the body, and they do not change the ending.
- Setting a finding aside is allowed for a stop: the owner sees it and decides. A real finding you set aside turns a send into a stop.
- Send the kept findings to the builder by your environment's reference, with the list you set aside, so that it knows what was seen.
- A fix is a new commit. Proof for the changed code runs again, by the "new commit and old proof" table in the `deliver` skill's proof reference.

## What the owner sees

- You write the outcome into the pull request body under **Review**: who reviewed, on which commit, the result, and each finding set aside with its reason. Read the body into a file beside your note (`gh pr view <n> --json body --jq .body`), edit it, and write it back with `gh pr edit <n> --body-file <file>`. Change nothing under Verification.
- Keep each reviewer's full report beside your note.
- Never resolve a review thread that a person opened, and check that the builder did not: `pr-status` prints the open threads, and the pull request page shows who resolved one.
- A close-out comment, or any other words posted where other people read them, is drafted for the owner. Post it only when the owner says so, and never while they are away.
