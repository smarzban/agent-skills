---
name: correct
description: Turn a mistake that agents keep making in a repo into a check that fails, so the next agent cannot make it. Tries a design change first, then a type, then a lint, then a test, and writes a rule only as a last resort. Use for "correct this", "agents keep doing X", "make this mistake impossible", "enforce this rule", "stop this happening again", and when the same review finding has come up twice.
---

# correct

The same mistake keeps being made in this repo. Change the repo so that the next agent cannot make it, or fails a check the moment it does. A written rule is the weakest fix: nothing fails when an agent skips it.

Assume every contributor is an agent that sees only the files it opened, copies the nearest example, and takes the shortest path that passes. A change that looks right from one file must be right for the whole repo.

Work on the current repo unless you are given a path.

## 1. Find the mistakes

Start from what you were told. If the owner named a mistake, that is the class; find its occurrences. Otherwise look for repeats:

- review comments on recent pull requests (`gh pr list --state all --limit 30`, then `gh pr view <n> --comments`);
- reverts and fix-up commits (`git log --oneline -200 | grep -i -E "revert|fix|oops|again"`);
- the rule table in `AGENTS.md`: every row whose "Enforced by" says "nothing yet";
- comments that explain a workaround, and **Gotchas** in `HANDOFF.md` files.

Group what you find into classes. A class counts when it has happened twice, or when the owner says it matters. For each class write one line: what goes wrong, and the commits, comments or files that show it.

With more than three classes, take the three most frequent and list the rest in the report.

## 2. Choose the fix, highest level first

For each class, take the first level that works and say why the ones above it did not.

1. **Design.** Make the wrong thing impossible to express. Give each piece of state one owner and each task one supported way. Hide internals so that the wrong import fails. Replace a list that is kept in step by hand with one source. Delete the old way and any dead code an agent would copy.
2. **Type.** Make the bad state fail to compile or to load.
3. **Lint or check.** A check in the verify command whose error message names the file, the function or the command to use instead. An agent reads that message and fixes itself.
4. **Test.** A test of the behaviour, which fails when the behaviour breaks and survives a refactor. Fix or delete a test that still passes when what it checks is broken.
5. **Rule.** One line in `AGENTS.md`, only for a judgement call that nothing can check. Add an example of the failure.

If the fix is a check, do not also write a rule for it. The rule table row is enough.

## 3. Fix and prove

- One class per commit.
- Prove each check against a real past mistake: apply the mistake (check out the old code, or write it back in), show that the check fails with the message you intended, then show that it passes on the fixed code. Paste both outputs in the pull request.
- The check runs in the repo's verify command, so that it runs the same way locally and in CI. Use the linter and the test framework the repo already has, or a small script in the repo. Adding a new tool or dependency needs the owner's yes.
- If the pattern is already common in the code, the check fails only on new occurrences: record the existing ones as a baseline in a file, and say how many there are.
- An exception goes on the offending line, with the reason, an expiry date and the name of the person who approved it. Never add an exception to get your own change through.

## 4. Keep the rule table

`AGENTS.md` holds a table that pairs each rule with what enforces it:

```markdown
| Rule | Enforced by |
|---|---|
| Tests never write to the real data folder | the `check-data-dir` step of the verify command |
```

- Add or update the row for each class you fixed. Name the check, not the intention.
- A row that said "nothing yet" and now has a check loses those words.
- Drop a row once the design makes its mistake impossible.
- If the repo has no such table, add it under a `## Rules` heading.

## 5. Ship

Open one pull request with the commits. It changes the verify command or the checks that guard the repo, so an agent never merges it alone: the ending is always stop, whatever was said. If the `deliver` skill is installed, follow it for the proof, the review and the pull request body.

When this runs with nobody present (a scheduled job, a coordinator): ask no questions, open the one pull request or none, and put what you could not decide in its description.

## Report

For each class: what goes wrong and the evidence, the level you chose, why each higher level did not work, the check and its two proof outputs, and any baseline or exception. Then the classes you found and did not fix.

`NOTICE` names the source this skill is adapted from.
