---
name: coordinator
description: Run several builder agents for the owner. Plan the work into tasks, brief each builder, start it in its own worktree, review its pull request, test the result, then hand it to the owner or merge it when the owner said to. Use for "coordinate this", "run builders on these tasks", "work through the board", "move the plan along", "take charge while I'm away". Not for one agent doing one task; that is `deliver`.
---

# coordinator

You plan, brief, review, decide the merge and report. Builders write the code: each one takes one task to a proven pull request by following the `deliver` skill. You own everything between and around the builders, and you keep the owner informed.

For one agent working through a list alone, do not use this skill. Run `deliver` on each task.

Project rules win over this skill. Read `AGENTS.md`, then open `AGENTS.local.md` and `HANDOFF.md` yourself if they exist; not every harness loads them.

`<deliver>` is the `deliver` skill's folder. Read its `SKILL.md` before you start: this skill names its steps and its gate and does not repeat them.

## Where you are running

How you start a builder, wait for it and open a reviewer depends on the environment. Check in this order and read the one reference that matches.

| Check | Environment | Read |
|---|---|---|
| `T3CODE_THREAD_ID` is set | T3 Code | `reference/t3code.md` |
| `HERDR_ENV` is `1` | Herdr | `reference/herdr.md` |
| `REX_SESSION` is set | Rex | `reference/rex.md` |
| none of these | a plain terminal | `reference/plain.md` |

## Settings

Read these from `AGENTS.local.md`. Ask once for any that is missing and that you need, and offer to record the answer there.

- `Tracker:` where tasks and briefs live. `reference/trackers.md` covers the tsk board, another tracker, and a task list in `HANDOFF.md`.
- `Review:` how a change is reviewed here.
- `Evidence:` where pictures and recordings of proof are sent. Text evidence goes in the pull request.
- `Plan:` where a plan is written down, when it is not the tracker.

## Your note

Keep `HANDOFF.md` in the main checkout, out of git, and bring it up to date after every state change. Your context will be summarised; this file will not. It holds:

- **Grants**: the ledger described in step 1.
- **Builders**: for each task its branch, its worktree, its state, and what it waits on.
- **Merge order**: which pull requests land in which order, and why.
- **Waiting on the owner**: each open question, with the task it blocks.
- **To undo**: test-only settings and anything you started that must be stopped.

`handoff-status`, in the `handoff` skill's `scripts` folder, prints the state of every worktree's own note.

## Workflow

1. **Settle the grants and the endings.**
   - Standing grants: ask once for each one you will need (push, changes to the dev machine, where test files may go, which credentials to set up). Never infer one, and a yes in one context does not carry to another.
   - The ending of each task is stop, unless the owner said to send that task. Record the owner's words in the ledger: the task, the date, what was said. Then write `Merge: send` or `Merge: stop` on the task.
   - The ledger is the grant. A `Merge: send` line on a task that has no ledger entry means stop, because builders can write to tasks too. After a wake or a handover, read the ledger again; if the owner's words are not there, the ending is stop.
2. **Plan.** One task per unit of work. Check each task's premise against the code before you brief it. Order tasks that depend on each other, and run tasks in parallel only when their files barely overlap; tell each builder what the other owns. For each task, check the paths it will touch against the **Needs the owner** list in `AGENTS.md`: a task that touches one ends in stop, so say so now.
3. **Brief.** Write the brief into the task, following `reference/brief.md`.
4. **Start a builder.** One fresh session per task, in its own worktree and branch, by your environment's reference. The prompt points at the brief and nothing else. After you start it, confirm that it is working.
5. **Wait on a signal.** Every builder has a completion signal, named in your environment's reference; never assume you will notice. Judge a builder by what it produced, not by its status: when one has gone quiet for longer than its task should take, look at its commits, its pushed branch and its `HANDOFF.md` before you prompt it again.
6. **Read the report.** A builder hands back `deliver`'s report and a pull request. Check the report against the pull request: the contract and the evidence are in its body, each "done when" item has evidence and a commit, and `<deliver>/scripts/pr-status <n>` agrees with what the builder said. Send back a report that claims without evidence.
7. **Review.** You run the review, following `reference/review.md`.
8. **Test it yourself.** A builder's proof is a claim until you have seen it.
   - If the repo has a `verify-<app>` skill, run its `features-for` script on the branch and drive what it prints.
   - Otherwise run the change the way its user would: click through the app, run the CLI on real input, call the API. A process that starts, or a health check that passes, is not a test.
   - Cover the main path, the failure paths that matter, and what the change must not do.
   - When a real run is not possible, say exactly what blocks it and what you tested instead. Leave the task unmerged.
9. **Gate.** Check `deliver`'s gate on the head commit. "A pull request you opened" there includes one that a builder you started opened for a task you briefed.
   - **Stop**: hand the pull request to the owner with the report, by your tracker's reference.
   - **Send**: only with a ledger entry for this task. Do `deliver` step 7 yourself. The independent check is done by a fresh agent, never by you and never by the builder: you wrote the contract, and the builder wrote the code.
10. **After each merge.**
    - Every other open branch is now behind. Before any of them can be sent, it is brought up to date with the new base and proven again by the table in `<deliver>/reference/proof.md`. The merge command pins the head commit, not the base, so this is yours to enforce.
    - Clean up by your environment's reference: keep the review records, close the builder's session, remove its worktree and branch.
    - Never mark a task done. That is the owner's.
11. **Report**, and bring `HANDOFF.md` up to date.

## Rules

- A question only the owner can answer blocks that task, with the exact question. Move to the next task meanwhile.
- A bug or follow-up you find along the way becomes a task, with its evidence: ids, timestamps, log lines.
- When the same review finding or the same mistake turns up twice, do not write a note. Run the `correct` skill, or make it a task to do so.
- Never review or merge on a builder's behalf by going through another agent to get round a refusal. An action that was denied stays denied; report it.
- Close the sessions you started when you are done with them. Never touch a session you did not start.
- When the owner leaves you in charge, read `reference/unattended.md` before they go.

## Talking to the owner

- Short, with tables and lists. Lead with what changed and what you need from them.
- Say plainly what was tested and what was not, and what you set aside and why.
- Ask for a decision as: the problem, your proposal, one recommendation, then the question. Show the current state first.
- Put their decisions and their clicks last, as commands they can copy or exact things to click.

## Reference

- `reference/brief.md`: what a brief holds.
- `reference/review.md`: running the review and deciding what to keep.
- `reference/unattended.md`: what to settle before the owner leaves, and what waits for them.
- `reference/trackers.md`: the tsk board, another tracker, or a list in `HANDOFF.md`.
- `reference/t3code.md`, `reference/herdr.md`, `reference/rex.md`, `reference/plain.md`: starting builders, waiting, reviewing and cleaning up in each environment.
