---
name: coordinator
description: Run several builder agents for the owner. Plan the work into tasks, brief each builder, start it in its own worktree, review its pull request, test the result, then hand it to the owner or merge it when the owner said to. Use for "coordinate this", "run builders on these tasks", "coordinate the board", "put builders on the plan", "take charge of the builders while I'm away". Not for one agent doing one task or working a list alone; that is `deliver`.
---

# coordinator

You plan, brief, review, decide the merge and report. Builders write the code: each one takes one task to a proven pull request by following the `deliver` skill. You own everything between and around the builders, and you keep the owner informed.

Project rules win over this skill. Read `AGENTS.md`, then open `AGENTS.local.md` and `HANDOFF.md` yourself if they exist; not every harness loads them.

`<deliver>` is the `deliver` skill's folder. Read its `SKILL.md` before you start: this skill names its steps and its gate and does not repeat them.

## Where you are running

How you start a builder, wait for it and open a reviewer depends on the environment. Check in this order, confirm with the probe, and read the one reference that matches. With the tsk board, a dispatched task also records its host (`dispatch.host`).

| Marker | Probe | Environment | Read |
|---|---|---|---|
| `T3CODE_THREAD_ID` is set | `delegate_task` is among your tools, possibly with a prefix or listed as not yet loaded | T3 Code | `reference/t3code.md` |
| `HERDR_ENV` is `1` | `herdr agent list` answers | Herdr | `reference/herdr.md` |
| `REX_SESSION` is set | `rex whoami --json` answers | Rex | `reference/rex.md` |
| none, or the probe fails | | a plain terminal | `reference/plain.md` |

A marker can be inherited by an agent that was started from inside one of these and does not have its tools. The probe decides. The markers were seen on 2026-11-12; two set at once has not been observed.

## Settings

Read these from `AGENTS.local.md`. What the owner told you in this conversation wins over it. Ask once for one that is missing only when a step needs it and no default below fits, and offer to record the answer there.

- `Tracker:` where tasks and briefs live. `reference/trackers.md` covers the tsk board, another tracker, and a task file.
- `Review:` how a change is reviewed here. Always settle this one with the owner before the first builder starts: with no line, ask ("How should changes be reviewed here: a fresh agent, a review step you name such as a panel, or you?"), and offer to record the answer. A fresh agent is the default only when nobody is there to ask.
- `Evidence:` where pictures and recordings of proof are sent. Text evidence goes in the pull request.
- `Plan:` where a plan is written down, when it is not the tracker.

## Your note

Keep `HANDOFF.md` in the main checkout, out of git (unless the repo's `.gitignore` lists it, add the name to the file that `git rev-parse --git-path info/exclude` prints), and bring it up to date after every state change. Start it with the header line the `handoff` skill gives, so that its script can read it. Your context will be summarised; this file will not. Builders never write to it; each keeps its own in its worktree. It holds:

- **Sends**: for each task the owner said to send, the date and their words, and where they said it.
- **Builders**: for each task its branch, its worktree, its pull request, its state, and what it waits on.
- **Merge order**: which pull requests land in which order, and why.
- **Waiting on the owner**: each open question, with the task it blocks.
- **To undo**: test-only settings and anything you started that must be stopped.

Keep your scripts, logs and the reviewers' reports in a folder that lasts, inside what the owner allowed, not a system temporary folder. `handoff-status`, in the `handoff` skill's `scripts` folder, prints the state of every worktree's own note.

## The grant to merge

The ending of every task is stop, unless the owner told you, in this conversation, to send that task, or the task carries a `Merge: send` line. A `Merge: stop` line always wins.

- The grant is the owner's words to you. **Sends** in your note records them; it is a pointer, not the grant, because a file can be written by anyone.
- Before a send, read the owner's words again in your own conversation, or the `Merge: send` line on the task. If you can find neither, because your context was summarised and the words are gone, or because you are a different agent that took over, the ending is stop until the owner confirms. A handover therefore loses every send that was only spoken; say so when you hand over.
- Never write a merge grant anywhere: not a `Merge:` line on a task, not in a brief, not in a pull request. A builder never holds one.
- A task you created yourself, such as a follow-up, ends in stop, even under "send them all".
- A task that touches a path on the repo's **Needs the owner** list ends in stop.
- A task or a pull request that carries the line `Origin: outside #<n>` ends in stop: it holds code from outside the project.
- A send covers the "done when" list the owner saw. If you change a task's goal or its list after they said to send it, the ending is stop until they confirm the new one.

## Workflow

1. **Ask for standing grants once**, and how changes are reviewed if no `Review:` line says: push, changes to the dev machine, where test files may go, which credentials to set up. Never infer one, and a yes in one context does not carry to another.
2. **Plan.** One task per unit of work. Check each task's premise against the code before you brief it. When a premise is wrong (the feature exists, the cause is elsewhere), write the task that is true and show the owner the difference; with nobody to ask, build the narrower task and end it in stop. Order tasks that depend on each other, and run tasks in parallel only when they change different files, or different functions of one file; tell each builder what the other owns, and settle the merge order now. Check the paths each task will touch against the **Needs the owner** list in `AGENTS.md`.
3. **Brief.** Write the brief into the task, following `reference/brief.md`.
4. **Settle the endings.** Show the owner each task's "done when" list, then ask which tasks to send. If they already said, do not ask again; show the lists and say that the send holds for these. Record each yes under **Sends**. Tell them which tasks will stop whatever they say, and why.
5. **Start a builder.** One fresh session per task, in its own worktree and branch, by your environment's reference. The prompt gives where the brief is, the repo's path and the path of the `deliver` skill to follow, and nothing else. After you start it, confirm that it started, by your environment's reference.
6. **Wait on a signal.** Every builder has a completion signal, named in your environment's reference; never assume you will notice. Judge a builder by what it produced, not by its status: when one has gone quiet for longer than its task should take, look at its commits, its pushed branch and its `HANDOFF.md` before you prompt it again.
7. **Read the report.** A builder hands back `deliver`'s report and a pull request. Check that the pull request's head branch is the one in your note, that the contract and the evidence are in its body with a commit for each item, and that `<deliver>/scripts/pr-status <n>` agrees with what the builder said. If its checks are not green, that is still the builder's work: send it back.
8. **Review.** You run the review, following `reference/review.md`, and you write its outcome into the pull request body under **Review**. The review and your own test may run at the same time.
9. **Test it yourself.** A builder's proof is a claim until you have seen it. Test in your own clean checkout of the head commit (`git worktree add --detach ../<repo>-test <sha>`), removed afterwards.
   - If the repo has a `verify-<app>` skill, run its `features-for` script on the branch and drive every feature it prints, including one listed only because a shared file changed. A feature you did not drive is named in the report, and the ending is stop.
   - Otherwise run the change the way its user would: click through the app, run the CLI on real input, call the API. A process that starts, or a health check that passes, is not a test.
   - Cover the main path, the failure paths that matter, and what the change must not do.
   - When a real run is not possible, say exactly what blocks it and what you tested instead. The ending is stop.
10. **Gate.** Check every line of `deliver`'s gate on the head commit. Its "nothing was loosened" and "no thread resolved" lines cover what the builder did as well as what you did.
    - **Stop**: hand the pull request to the owner with the report and the merge command, by `reference/trackers.md`. Keep its worktree until it is merged or closed.
    - **Send**: only when the owner said to send this task and you have read their words again. Do `deliver` steps 7.1 to 7.4. The independent check is done by a fresh agent, never by you and never by the builder: you wrote the contract, and the builder wrote the code. It is skipped, as `deliver` step 7.1 says, when the owner said to merge after you handed them this pull request at this head commit. Clean up by step 11, not by `deliver` step 7.5.
11. **After each merge**, by you or by the owner.
    - Bring your main checkout up to date, and run the repo's verify command on the merged base.
    - Every other open branch that you started is now behind; `pr-status` does not show that. Have a builder bring it up to date, by "Sending work back" in your environment's reference, and read its report as in step 7. On the new head, the verify command, your own test and, for a send, the independent check run again; the review stands or not by the "new commit and old proof" table in `<deliver>/reference/proof.md`. Give the owner a new merge command for any pull request already handed to them.
    - Clean up the merged task by your environment's reference: close the builder's session, then remove its worktree, its local branch and its remote branch. Removing a worktree deletes its ignored files, the builder's `HANDOFF.md` and `.verify/` among them; the evidence that matters is already in the pull request. While the owner is away, keep the worktree until they are back. Never remove your own note or your own main checkout.
    - Never mark a task done. That is the owner's.
12. **Report**, and bring `HANDOFF.md` up to date. One row per task: the pull request, the head commit, the `pr-status` verdict, the ending and why, what you tested, what you did not. Then the follow-up tasks you wrote, what you left behind, and last the commands the owner needs.

## Rules

- A question only the owner can answer blocks that task, with the exact question. Move to the next task meanwhile.
- A bug or follow-up you find along the way becomes a task, with its evidence: ids, timestamps, log lines.
- When the same review finding or the same mistake turns up twice, do not write a note. Run the `correct` skill, or make it a task to do so.
- An action that was denied stays denied. Never get round a refusal by asking another agent to do it; report it.
- A merge goes only through `deliver` step 7.3. Never use a tracker's or a host's own merge shortcut.
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
- `reference/trackers.md`: the tsk board, another tracker, or a task file.
- `reference/t3code.md`, `reference/herdr.md`, `reference/rex.md`, `reference/plain.md`: starting builders, waiting, sending work back, reviewing and cleaning up in each environment.
