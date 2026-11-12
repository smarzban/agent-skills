---
name: correct
description: Turn a mistake that agents keep making in a repo into a check that fails, so the next agent cannot make it. Tries a design change first, then a type, then a lint, then a test, and writes a rule only as a last resort. Use for "correct this so it can't happen again", "agents keep doing X", "make this mistake impossible", "enforce this rule". Not for a one-off fix or a typo, and not for a finding inside a pull request that is still under review.
---

# correct

The same mistake keeps being made in this repo. Change the repo so that the next agent cannot make it, or fails a check the moment it does. A written rule is the weakest fix: nothing fails when an agent skips it.

Assume every contributor is an agent that sees only the files it opened, copies the nearest example, and takes the shortest path that passes. A change that looks right from one file must be right for the whole repo.

Work on the current repo unless you are given a path.

## Before you start

- The ending is stop: this skill never merges, whatever was said. It changes the checks that guard the repo, and the owner merges those.
- Before the first commit, start a branch in a worktree of its own. Never commit on the default branch. With no remote, leave the branch and report.
- If the `deliver` skill is installed, follow all of it, with the ending fixed at stop: write "Ending: stop (fixed by `correct`)" in `HANDOFF.md`, so that a later "send it" is not read as a grant. The contract has one group of items per class: the check fails on the mistake, it passes on the fix, and behaviour the class does not concern is unchanged. Use `deliver`'s report, with the per-class account from **Report** below as its "Done when". A baseline makes `deliver`'s "nothing was loosened" line false; say so, and it costs nothing because the ending is stop.
- What the owner must decide goes in one place: under `## For the owner to decide` in the pull request body, and again in the report.
- If a pull request from an earlier run of this skill is still open (branch `correct/<name>`), add to it; do not open a second one.

## 1. Find the mistakes

Start from what you were told. If the owner named a mistake, that is the class; find its occurrences. When it left no trace in the repo (it happened in an agent's own shell), the owner's account is the evidence; say so.

Otherwise look for repeats:

- review comments on recent pull requests. With no remote, skip this source.

  ```sh
  gh pr list --state all --limit 30
  gh pr view <n> --comments                         # the conversation
  gh api repos/{owner}/{repo}/pulls/<n>/comments    # the comments on lines of the diff
  ```

- reverts and fix-up commits: `git log --oneline -200 | grep -i -w -E "revert|fix|fixes|fixed|fixup|oops|again"`. Read the commits it prints; most will be ordinary fixes.
- the rule table in `AGENTS.md`: a row whose "Enforced by" says "nothing yet" is a candidate once you find an occurrence of its mistake. Skip a row marked "judgement".
- comments that explain a workaround, and **Gotchas** in the `HANDOFF.md` of each worktree (`git worktree list`).

Group what you find into classes. A class counts when it has happened twice, or when the owner says it matters. For each class write one line: what goes wrong, and the commits, comments or files that show it.

With more than three classes, take the three most frequent and list the rest in the report. If one class stalls, ship the ones that are finished and proven, and leave the stalled one out of the branch and in the report.

## 2. Choose the fix, highest level first

For each class, take the first level that works and say why the ones above it did not.

1. **Design.** Make the wrong thing impossible to express, or refuse it at the moment it is attempted. Give each piece of state one owner and each task one supported way. Hide internals so that the wrong import fails. Replace a list that is kept in step by hand with one source. Delete the old way and any dead code an agent would copy.
2. **Type.** Make the bad state fail to compile or to load.
3. **Lint or check.** A check whose error message names the file, the function or the command to use instead. An agent reads that message and fixes itself.
4. **Test.** A test of the behaviour, which fails when the behaviour breaks and survives a refactor.
5. **Rule.** One line in the rule table, only for a judgement call that nothing can check, with an example of the failure. Write "judgement; nothing can check" in its "Enforced by" column, so that a later run leaves it alone.

Limits on the choice:

- Prefer a fix that stops the mistake where the harm happens over one that reports it afterwards. A check in the verify command is too late for a mistake that deletes or overwrites something when it runs.
- Anything a user, a script or another repo can observe is the owner's to change: output, exit codes, defaults, public API, file formats. A new check in the verify command is the one exception; that is what this skill is for. Take the strongest fix that leaves those as they are, say plainly what it still lets through, and put the stronger fix forward for the owner to decide. When nobody is present, make no design-level change that alters any of them.
- A higher level that covers only part of the class can be joined with a lower one that covers the rest.
- If the fix is a check, do not also write a rule for it. The rule table row is enough.
- A test that still passes when what it checks is broken may be fixed, after you show it passing with the behaviour broken. Deleting a test is the owner's decision.

## 3. Fix and prove

- One class per commit. Fixes that come out of review are further commits.
- Prove each check against the mistake as it was made. Do it in a throwaway worktree (`git worktree add --detach ../<repo>-mistake <sha>`), removed afterwards; the branch you ship never holds the mistake. Check out the old code there, or write the mistake back in the way it was described. Show that the check fails with the message you intended, then show that it passes on the fixed code. Paste both outputs in the pull request, and say what tree each ran on. For `deliver`, this pair is the evidence for the class, at ladder step 4. For a design fix, prove the design (the wrong thing is refused) and the test that guards it.
- If making the mistake is itself harmful, make it in isolation as well: a temporary home folder, a copy of the data, a container. Check afterwards that nothing real was touched.
- A check that watches or blocks file writes, processes or the network can do damage while it is half built. Develop it with the home and temporary folders pointed at a scratch folder you will delete.
- The check runs in the repo's verify command, so that it runs the same way locally and in CI. A repo with no verify command gets the check in its test command. A repo with neither gets the check as a script, and wiring it in is for the owner to decide.
- Use the linter and the test framework the repo already has, or a small script in the repo. Adding a new tool or dependency needs the owner's yes.
- If the code already breaks the new check in places you cannot fix in this change, or may not (the fix would change what users see), the check fails only on new occurrences. Record the existing ones in a baseline file, keyed by file and by the offending text or symbol, never by line number, and report the count. A baseline is an allowlist; name it as one in the pull request.
- An exception goes on the offending line, with the reason, an expiry date and the name of the person who approved it. The owner asking for the correction is not that approval. With nobody to approve, add no exception and list the case for the owner.

## 4. Keep the rule table

The repo's agent instruction file holds a table that pairs each rule with what enforces it. That file is `AGENTS.md`; if the repo has only a `CLAUDE.md`, use that, and if it has neither, report it and do not create one (`repo-setup` does).

```markdown
| Rule | Enforced by |
|---|---|
| Tests never write to the real data folder | the `check-data-dir` step of the verify command |
```

- Add or update the row for each class you fixed. Name the check, not the intention.
- A row that said "nothing yet" and now has a check loses those words.
- A design fix that a later edit could undo keeps its row, naming the test that guards it. Drop a row only when nothing in the repo could bring the mistake back.
- If the file has no such table, add it under a `## Rules` heading.

## 5. Ship

Open one pull request with the commits, on a branch named `correct/<what it fixes>`. Without `deliver`: run the repo's verify command on the head commit, have a fresh agent review the diff, and put both proof outputs in the body.

When this runs with nobody present (a scheduled job, a coordinator): ask no questions, open the one pull request or none, and put what you could not decide under `## For the owner to decide`.

## Report

For each class: what goes wrong and the evidence, the level you chose, why each higher level did not work, the check and its two proof outputs, and any baseline. Then what is for the owner to decide, and the classes you found and did not fix.
