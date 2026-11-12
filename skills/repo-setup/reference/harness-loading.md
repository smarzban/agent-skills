# What each harness loads

Observed facts, each with the version and date it was observed on. A cell with no run count is one run. A harness update can change any of them, so probe again before you rely on one. Anything not probed is marked untested and is not a basis for a decision.

## Probe method

1. Make a throwaway folder outside any repo, run `git init` in it, and check that no parent folder holds an instruction file.
2. Put a distinct codeword in each file under test, for example `The codeword for the root instruction file is QUARTZ-7f3a91.`
3. Run the harness headless with every tool blocked, and ask it to list the codewords in its loaded instructions, or `NONE`. Tools must be blocked: with tools, the agent can read the file as an action, which proves nothing about loading. Where a harness cannot block tools, read its event stream and discard the run if any tool call appears.
4. Run the same prompt in an empty folder. It must answer `NONE`; otherwise a global file is leaking into the result.

```sh
claude -p "$PROMPT" --tools "" --strict-mcp-config --no-session-persistence
codex exec --skip-git-repo-check --sandbox read-only --ephemeral --json "$PROMPT" </dev/null
pi -p --no-session --no-tools --no-skills --no-extensions "$PROMPT" </dev/null
grok inspect        # lists the instruction files it found; needs no sign-in
```

## Results, 2026-11-12

| Fact | Claude Code 2.1.322 | Codex 0.167.1 | Pi 1.5.2 |
|---|---|---|---|
| Loads `AGENTS.md` with no other instruction file present | yes, 3 of 3 runs | yes | yes |
| Expands `@AGENTS.local.md` written in `AGENTS.md` | yes | no | no |
| A missing `@AGENTS.local.md` target | skipped, no error | line is plain text | line is plain text |
| `AGENTS.local.md` is a link to a file outside the folder | import not loaded, 3 of 3 runs | not probed | not probed |
| Follows "if `AGENTS.local.md` exists, read it" by reading the file (tools on) | not needed | yes, 1 of 1 run | yes on a file task, no on a one-line question |
| Started in `packages/app/`: loads root and nested `AGENTS.md` | yes, both | yes, both | yes, both |
| Started at the root: loads the nested `AGENTS.md` at start | no | no | no |
| `CLAUDE.md` and `AGENTS.md` both present, no import | loads `CLAUDE.md` only, 3 of 3 runs | not probed | not probed |
| `CLAUDE.md` containing `@AGENTS.md` | loads `AGENTS.md` and its import, 3 of 3 runs | not applicable | not applicable |
| `AGENTS.override.md` beside `AGENTS.md` | not applicable | loads the override only | not probed |

Grok 1.0.67: `grok inspect` in a folder it does not trust lists only the global instruction file and reports `Project trusted: no`. A model run was not possible (not signed in from the shell), and a trusted folder was not tested. Whether Grok loads `AGENTS.md` in a trusted folder is untested.

Cursor (CLI 2026.10.01): untested. The CLI was not signed in from the shell. Whether Cursor has a private, uncommitted rules file is also untested.

Not probed in any harness: whether a nested `AGENTS.md` is picked up later in a session that started at the root, and the size at which a harness truncates an instruction file.

## What follows from the results

- `CLAUDE.md` is not needed for Claude Code to read `AGENTS.md`, so this skill does not create one. Older Claude Code versions were not probed; an owner whose collaborators run one can ask for the pointer.
- An existing `CLAUDE.md` hides `AGENTS.md` from Claude Code unless it imports it. After a migration it holds the single line `@AGENTS.md`.
- The private file reaches Claude Code through the import, and only when it is a real file in the folder. `scripts/worktree-setup` therefore copies it into each worktree.
- The private file reaches Codex and Pi only when the agent obeys the "read it" line, which is likely on real work and not guaranteed. Do not put a rule in `AGENTS.local.md` that must hold in every harness.
- A nested `AGENTS.md` is certain to load only for a session started in its folder. The root file lists the nested files so that a session started at the root reads them.
- `AGENTS.override.md` replaces `AGENTS.md` in Codex. Never create it.
