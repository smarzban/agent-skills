---
name: handoff
description: Write or pick up HANDOFF.md, the resume note that lets a new session, an agent in a different harness, or a coordinator continue a branch's work cold. Use when asked to "write a handoff", "hand off", "save state for a new session", "pick up where we left off", "resume", or "what is each worktree doing", and when starting work in a worktree that contains HANDOFF.md.
---

# handoff

`HANDOFF.md` is a snapshot of one branch's work, written by an agent for the next agent. It sits in the root of the worktree, is ignored by git, and is overwritten in full on every write. It holds what git, the task and the PR do not: the intent, what is verified, the next action, and why files are uncommitted.

Keep durable facts out of it. A fact that will still be true next month (a convention, a tool quirk) goes in the repo's `AGENTS.md`, or in `AGENTS.local.md` if it is private.

## Write

1. Read the existing `HANDOFF.md`, if there is one. Carry forward every item that is still true, including items you cannot confirm from your own context. Drop only what you know is finished or wrong.
2. Get the facts from commands, not memory: `git rev-parse --abbrev-ref HEAD`, `git rev-parse --short HEAD`, `git status --porcelain`.
3. Run `git check-ignore -q HANDOFF.md`. If the file is neither ignored nor tracked, add `HANDOFF.md` to `.gitignore` and say that you did.
4. Write the file from the template. Put an item under **Verified** only if you ran its command in this session and it passed. This includes items carried forward: run the command again, or move the item to **Unverified**. Start **Next** with one exact command or file path.
5. Reply with the path and the first next action.

```markdown
# HANDOFF
_<YYYY-MM-DD> · <harness> · <branch> @ <short-sha> · <n> uncommitted_

## Goal
<What this branch is for, in one or two lines. Link the task or PR.>

## State
- Verified: <what works> (`<command that proved it>`)
- Unverified: <done, not yet proven>
- In progress: <started, not finished>

## Next
1. <First action: an exact command or file path.>
2. <The rest, in order.>

## Decisions
- <What was decided and why. What was deliberately left undone.>

## Uncommitted
- <file>: <why it is not committed>

## Gotchas
- <Only what git and the code do not show.>
```

Omit a section that has nothing in it.

## Pick up

1. Read `HANDOFF.md`. Compare the commit in its header with `git rev-parse --short HEAD`. If they differ, run `git log --oneline <note-sha>..HEAD` and `git status --porcelain`, and state what changed after the note was written.
2. Treat the note as authoritative. Do not repeat work it lists as done, and do not reopen its decisions.
3. Before you build on a **Verified** item, run its command once. If the command fails, say so and make that the first task.
4. Start at **Next**. Say in one or two lines where you are resuming.

If there is no `HANDOFF.md`, say so. Reconstruct the state from `git log`, the task and the PR, and do not invent the rest.

## Coordinator

Run `scripts/handoff-status` from this skill's folder, inside any worktree of the repo. It prints one block per worktree: branch, commit, the note's header, whether the branch moved after the note, and the first **Next** line.

A coordinator keeps its own note in the main checkout: which builders are running, what each one waits on, and the merge order.

## End of a branch

Delete the note when the branch merges. Before you remove a worktree whose work is unfinished, copy **Next** and **Decisions** to the task or the PR.
