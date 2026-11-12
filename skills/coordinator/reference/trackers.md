# Where tasks live

Use what the `Tracker:` line in `AGENTS.local.md` says. Without one:

| Setup | Task list | The brief goes in |
|---|---|---|
| `tsk` is on the path, and the project uses no other tracker | The tsk board | The task's notes |
| The project uses another tracker | That tracker | The issue body |
| Neither | A `TASKS.md` file in the main checkout, out of git | A section per task |

A task holds the brief, its status, and who it waits on. It never holds a merge grant.

## The tsk board

Run `tsk guide` first, or read the `tsk-cli` skill. It owns every command; this file names only what a coordinator does with them. The commands below were checked against `tsk help` in tsk 0.13.0 and the tsk repo's `tsk-cli` skill, version 1.7.0, on 2026-11-12.

| To do this | Use |
|---|---|
| Read a task in full | `tsk list T12 --json` |
| Hold a task until another is done | `tsk edit T13 --after T12` |
| Set who builds it and from which branch | the assignee and the base, by `tsk guide` |
| Start a builder in Herdr or Rex | `tsk dispatch T12` (see `herdr.md` or `rex.md`) |
| See that a builder finished | the task's status is `review`, waiting on you, or `blocked` |
| Send findings back to a dispatched builder | `tsk reply T12 "…" --send`, in Herdr or Rex |
| Hand the review to the owner | `tsk edit T12 --on you` |
| Clean up after a merge | `tsk clean T12` |

- The builder sets `review` with `--on` naming you when it hands back. Setting `review_on` under the project in tsk's `config.toml` puts your name in the default prompt.
- After you send findings back, the status stays `review`. The signal for the next round is a new head commit on the pull request; ask the builder to reply on the task when it has pushed.
- `tsk edit T12 --on you` answers `not-blocked` when the builder opened no review round, as a builder started outside tsk will not have. Open one yourself: `tsk status T12 review --done "<the first line of the report>" --check "<what the owner should check>"`, which waits on the owner by default.
- `tsk reply` and `tsk dispatch` are not safe to repeat. Check the task with `tsk list T12 --json` before you run one again.
- `tsk dispatch` works only inside Herdr or Rex. Elsewhere it answers `no-host`; start the builder by your environment's reference and set the task started with `tsk status T12 started --no-dispatch`.
- Never use `tsk clean --merge` or `tsk status … done --merge`. They merge without pinning the commit and set the task done. Never set a task `done`; the owner does that, from the board.

## Another tracker

Put the brief in the issue body. Use the tracker's own status for the completion signal, or have the builder message you. Say in the brief exactly how to hand back.

## A task file

Keep `TASKS.md` in the main checkout, out of git, with one section per task: the brief, the state, and what it waits on. It is a separate file from your `HANDOFF.md`, because builders read it and must not read or write your note. Give each builder the path and its section's heading.
