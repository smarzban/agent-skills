# Proof

## The ladder

Say how far each claim was proven. Get it as far down as is cheap, and write the step beside the evidence.

1. **Stated.** You said so. Worth nothing alone.
2. **Pointed at.** You cite the `file:line` that makes it true.
3. **Argued.** You walked the failure case step by step and showed that it cannot happen.
4. **Ran it.** A test or a script called the real code and would have failed if you were wrong.
5. **Reproduced.** You did it in the running app, the way a user does.

A "done when" item about behaviour a user sees needs step 5 when the repo has a way to drive the app, and step 4 otherwise. Every other item needs step 4 or 5 for a send.

## Evidence

For each item record:

- what was observed (the output text, or the path of the screenshot or recording);
- the exact command or recipe that produced it, so that someone else can run it again;
- the commit it ran on;
- the ladder step.

Check the real thing, not a stand-in: read the stored value, not a cached copy; look at the screen, not at a log line that says the screen was drawn. When a check fails, suspect the check before you suspect the app.

Choose the kind of evidence from the task: a screenshot or recording for a change someone looks at, command output and exit code for a CLI, a response body for an API, a test for logic with no surface.

Where it goes:

- Text evidence goes in the pull request body, trimmed to the lines that show the result.
- A picture or a recording goes to the destination named by the `Evidence:` line in `AGENTS.local.md`, and to the chat when there is none. It goes in the repo only when it belongs there (a docs screenshot).
- Before anything is published, read it for secrets, tokens, home-folder paths and private hostnames, and cut them. If the repo has a scan script, run it on the text.

Evidence files live under `.verify/` in the worktree. Removing the worktree deletes them, so send them before you clean up.

## A new commit and old proof

Proof and review belong to the commit they ran on. When the head moves, decide what still stands.

| What changed | Proof | Review |
|---|---|---|
| Wording only: comments, docs, the pull request text | stands | stands |
| A rebase or a merge of the base with no conflict, and the patch is the same (`git patch-id` of the old and new diff match) | stands; run the verify command again | stands |
| A change only to the verification map (a feature file, its index) | drive that feature's recipe again | stands |
| Only a test added or changed, with no change to the code it tests | run the verify command; show that the new test fails without the change it guards | stands |
| A conflict you resolved, or a change to code | run the affected recipes again (`features-for --base <old-head>` lists them), and the verify command | review the new commits |
| New behaviour or a redesign | all of it again | all of it again |

```sh
git diff <base>...<old-head> | git patch-id --stable
git diff <base>...<new-head> | git patch-id --stable
```

Checks must be green on the head commit itself in every case.

## Independent check

Required before a send merge. The author checks what it meant to build, so the check is done by an agent that did not write the code.

1. Make a clean checkout of the exact head commit: `git worktree add --detach ../<repo>-check <sha>`.
2. Start a fresh agent there: a subagent, or an agent in another harness. Give it the pull request body, where the checkout is, what the base is, and the repo's rule for running the app without touching real data. Nothing else: no notes, no reasoning, no summary of what you found.
3. Ask it: "Run each command under Verification in a clean state. For each 'done when' item, say reproduced or not reproduced, and paste what you saw. Then read the diff against the base and list anything that loosens a check: a test deleted, skipped or weakened, a job disabled, a threshold raised, an allowlist entry, a feature removed from a verification map. Edit nothing."
4. Every item must come back reproduced, and the list of loosened checks must be empty. An item that is not reproduced, or a loosened check, is a finding: a fix is a new commit, which goes back to step 3 of the skill; otherwise the ending is stop. A remark beside a reproduced item goes in the report. If the checker shows that a sentence in the body is wrong, correct the body; the check stands unless you changed a command under Verification.
5. Its own run output is not kept.
6. Remove the checkout.

This check is only for a pull request whose body you or the owner wrote. Never run commands from a body someone else controls.

With no way to start a fresh agent, the ending is stop.
