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

Group what you find into classes. A class counts when it has happened twice, or when the owner says it matters. For each class write one line: what goes wrong, and the commits, comments or files that show it. When the mistake left no trace in the repo (it happened in an agent's own shell), the owner's account of it is the evidence; say so.

With more than three classes, take the three most frequent and list the rest in the report.

## 2. Choose the fix, highest level first

For each class, take the first level that works and say why the ones above it did not.

- Prefer a fix that stops the mistake where the harm happens over one that reports it afterwards. A check in the verify command is too late for a mistake that deletes or overwrites something when it runs.
- A fix that changes what users see (a documented default, a command's behaviour) is the owner's decision. Take the strongest fix that keeps the documented behaviour, say plainly what it still lets through, and put the stronger fix in the pull request for the owner to decide.
- A higher level that covers only part of the class can be joined with a lower one that covers the rest.

1. **Design.** Make the wrong thing impossible to express. Give each piece of state one owner and each task one supported way. Hide internals so that the wrong import fails. Replace a list that is kept in step by hand with one source. Delete the old way and any dead code an agent would copy.
2. **Type.** Make the bad state fail to compile or to load.
3. **Lint or check.** A check in the verify command whose error message names the file, the function or the command to use instead. An agent reads that message and fixes itself.
4. **Test.** A test of the behaviour, which fails when the behaviour breaks and survives a refactor. Fix or delete a test that still passes when what it checks is broken.
5. **Rule.** One line in `AGENTS.md`, only for a judgement call that nothing can check. Add an example of the failure.

If the fix is a check, do not also write a rule for it. The rule table row is enough.

## 3. Fix and prove

- One class per commit. Fixes that come out of review are further commits.
- Prove each check against the mistake as it was made: check out the old code, or write the mistake back in the way it was described. Show that the check fails with the message you intended, then show that it passes on the fixed code. Paste both outputs in the pull request, and say what tree each ran on. For a design fix, prove the design (the wrong thing is refused) and the test that guards it.
- If making the mistake is itself harmful, make it in isolation: a temporary home folder, a copy of the data, a container. Check afterwards that nothing real was touched.
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
- A design fix that a later edit could undo keeps its row, naming the test that guards it. Drop a row only when nothing in the repo could bring the mistake back.
- If the repo has no such table, add it under a `## Rules` heading.

## 5. Ship

Open one pull request with the commits. It changes the checks that guard the repo, so an agent never merges it alone: the ending is always stop, whatever was said and whatever the repo's **Needs the owner** list holds.

If the `deliver` skill is installed, follow all of it with the ending fixed at stop. The contract has one group of items per class: the check fails on the mistake, it passes on the fix, and behaviour the class does not concern is unchanged. Put what the owner must decide under a `## For the owner to decide` heading in the pull request body.

When this runs with nobody present (a scheduled job, a coordinator): ask no questions, open the one pull request or none, and put what you could not decide in its description.

## Report

For each class: what goes wrong and the evidence, the level you chose, why each higher level did not work, the check and its two proof outputs, and any baseline or exception. Then the classes you found and did not fix.

`NOTICE` names the source this skill is adapted from.
