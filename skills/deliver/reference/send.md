# Send

Only when every line of the gate in `SKILL.md` holds. Do these in order.

1. Have a fresh agent re-run the proof ("The independent check", below). With no fresh agent available, the ending is stop. Skip this step when the owner's words to merge came after a **Ready for you** report on this pull request, and the head commit is still the one in that report's merge command, read again in this conversation. If you cannot read the report and the owner's words there, do not skip. The steps below still apply.
2. Straight after it, run `git fetch`, `<skill>/scripts/pr-status --head <sha>` and `<skill>/scripts/needs-owner --head <sha> --base origin/<base>` again. The check takes minutes, and a review or a push can arrive in that time. Anything other than `READY` and exit 0 sends you back in `SKILL.md`: a new commit to step 3, a new finding to step 4. Then `git merge-base --is-ancestor origin/<base> <sha>` must exit 0: `pr-status` does not show a branch that is behind its base. If it does not, bring the branch up to date; that is a new commit.
3. Write down each gate line and its result in `HANDOFF.md`. Then merge the exact commit that was proven, by the method of the repo's earlier merges, or squash when there are none: `gh pr merge <n> --squash --match-head-commit <sha>` (or `--merge`). Never use `--auto` or `--admin`.
4. Read it back: `<skill>/scripts/pr-status <n>` must print `MERGED` into the default branch. With gh 2.101.0 a merge command can return success when the pull request was only queued. If it is not merged, wait and run it again; do not clean up, and report it if it stays unmerged.

Then go back to step 7 of `SKILL.md` for the clean-up.

## The independent check

Required before a send merge, except as step 1 above says. The author checks what it meant to build, so the check is done by an agent that did not write the code.

1. Make a clean checkout of the exact head commit: `git worktree add --detach ../<repo>-check <sha>`. Where your harness gives a fresh agent its own checkout, that one is used instead, set to the head commit.
2. Start a fresh agent there: a subagent, or an agent in another harness. Give it the pull request body (as text in its task, or a file beside the checkout), where the checkout is, what the base is, and the repo's rule for running the app without touching real data. Nothing else: no notes, no reasoning, no summary of what you found.
3. Ask it: "Run each command under Verification in a clean state. For each 'done when' item, say reproduced or not reproduced, and paste what you saw. Then read the diff against the base and list anything that loosens a check: a test deleted, skipped or weakened, a job disabled, a threshold raised, an allowlist entry, a feature removed from a verification map, a rule in a verification skill relaxed. Leave the checkout as you found it: a command under Verification may change a file and put it back, and you change nothing else."
4. Every item must come back reproduced, and the list of loosened checks must be empty. An item that is not reproduced, or a loosened check, is a finding: a fix is a new commit, which goes back to step 3 of the skill; otherwise the ending is stop. A remark beside a reproduced item goes in the report. If the checker shows that a sentence in the body is wrong, correct the body; the check stands unless you changed a command under Verification.
5. Its own run output is not kept.
6. Remove the checkout.

This check is only for a pull request whose body you or the owner wrote, or one written by a builder you started, after you have read each command under Verification against the contract. Never run commands from a body someone else controls.

With no way to start a fresh agent, the ending is stop.
