# Where tasks live

Use what the `Tracker:` line in `AGENTS.local.md` says. Without one:

| Setup | Task list | The brief goes in |
|---|---|---|
| `tsk` is on the path, and the project uses no other tracker | The tsk board | The task's notes |
| The project uses another tracker | That tracker | The issue body |
| Neither | A **Tasks** section in your `HANDOFF.md` | A subsection per task |

Whichever it is, a task holds: the brief, its `Merge:` line, its status, and who it waits on.

## The tsk board

Run `tsk guide` first, or read the `tsk-cli` skill. It owns every command; this file names only what a coordinator does with them. The commands below were read from the tsk repo's `tsk-cli` skill, version 1.7.0, on 2026-11-12.

| To do this | Use |
|---|---|
| Read a task in full | `tsk list T12 --json` |
| Hold a task until another is done | `tsk edit T13 --after T12` |
| Set who builds it and from which branch | the assignee and the base, by `tsk guide` |
| Start a builder in Herdr or Rex | `tsk dispatch T12` (see `herdr.md` or `rex.md`) |
| See that a builder finished | the task's status is `review`, waiting on you, or `blocked` |
| Answer a builder | `tsk reply T12 "…" --send`, in Herdr or Rex |
| Hand the review to the owner | `tsk edit T12 --on you` |
| Clean up after a merge | `tsk clean T12` |

- The builder sets `review` with `--on` naming you when it hands back. Setting `review_on` under the project in tsk's `config.toml` puts your name in the default prompt.
- `tsk reply` and `tsk dispatch` are not safe to repeat. Check the task with `tsk list T12 --json` before you run one again.
- `tsk dispatch` works only inside Herdr or Rex. Elsewhere it answers `no-host`; start the builder by your environment's reference and set the task started with `tsk status T12 started --no-dispatch`.
- Never set a task `done`. The owner does that, from the board.
- The notes are the owner's. Ask whether to add to them or replace them before you write into notes the owner wrote.

## Another tracker

Put the brief in the issue body and the `Merge:` line with it. Use the tracker's own status for the completion signal, or have the builder message you. Say in the brief exactly how to hand back.

## A list in HANDOFF.md

Add a **Tasks** section to your note, with one subsection per task: the brief, the `Merge:` line, the state, and what it waits on. Builders read the brief from the path you give them and do not edit your note; each keeps its own `HANDOFF.md` in its worktree.
