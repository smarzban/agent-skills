---
name: repo-setup
description: Set up or audit a repo's working baseline for people and agents. Public AGENTS.md plus a gitignored AGENTS.local.md, ignore rules, one verify command that CI also runs, and a worktree setup script. Migrates an existing CLAUDE.md without losing a line. Use for "set up this repo", "repo setup", "bootstrap the repo", "add AGENTS.md", "migrate CLAUDE.md to AGENTS.md".
---

# repo-setup

Bring a repo, empty or existing, to a baseline that any agent harness can work in. This skill writes machinery and facts it can check: commands, ignore rules, scripts, CI. It writes no prose. The project overview, the README and the docs belong to `writing-readmes` and `writing-docs`.

Work on the current repo unless you are given a path.

## Steps

1. Run `scripts/repo-audit` from this skill's folder and read every file it lists as existing. On an empty repo the audit reports everything missing; run it anyway.
2. Settle the repo type, because it selects the optional files: solo or team (the audit's author count, an existing `CODEOWNERS`), public or private. Ask when the evidence does not say.
3. Show the owner the audit and one proposal: each file to create, each edit to an existing file as a diff, and each owner decision. Wait for a yes. If the owner already told you to proceed without asking, print the proposal and continue.
4. Write what was agreed, from `reference/templates.md`.
5. Check the result:
   - `scripts/verify` exits 0, and the CI workflow runs that same file.
   - `scripts/repo-audit` now reports both ignore rules as present.
   - `git worktree add --detach ../<repo>-probe && (cd ../<repo>-probe && scripts/worktree-setup)` succeeds; then run `git worktree remove ../<repo>-probe`. A repo with no commits cannot have a second worktree: report this check as not run. A worktree holds only committed files, so if the new scripts are not committed yet, copy them into the probe and say that you did.
   - `AGENTS.md` is inside its budget. Over budget is a warning for the report, not a failure.
6. Report: created, edited (with the diff), left alone, owner decisions still open, and anything a check did not prove.

## Files

| Tier | File | When |
|---|---|---|
| Core | `AGENTS.md`, `AGENTS.local.md` | always |
| Core | `.gitignore` | always: `AGENTS.local.md`, `HANDOFF.md`, and the rules for the detected stack |
| Core | `scripts/verify` | when the repo has at least one real check |
| Core | CI workflow that runs `scripts/verify` | when `scripts/verify` exists |
| Core | `scripts/worktree-setup` | always |
| Optional | issue and pull request templates, `CODEOWNERS` | team repos |
| Optional | `.editorconfig`, `.gitattributes` | when the owner wants them |
| Optional | secret-scanning baseline | public repos, and any repo that holds credentials locally |

`HANDOFF.md` itself is not created here. The `handoff` skill writes one per worktree; this skill only makes sure git ignores it.

## Rules

- Never replace an existing file. Add the missing lines, keep the rest, and show the diff first.
- Write a fact or leave the line out. Take build, test and lint commands from the CI config and the package scripts that the audit prints, and run each one before you write it down. A repo with no checks yet gets no `scripts/verify` and no CI file. Report them as waiting for a stack; do not write a placeholder.
- Use the repo's existing verify entry point if it has one (`make check`, `npm run verify`). Otherwise create `scripts/verify`. In both cases CI calls the same command a person runs.
- Owner decisions are flagged and never invented: the licence, the security policy and its reporting channel, the code of conduct, `CODEOWNERS` entries, and remote settings (visibility, branch protection, hosted secret scanning). For a remote setting, give the exact command and let the owner run it. One exception: when the repo has a single author, propose `* @<that-owner>` for `CODEOWNERS` and ask for confirmation.
- Never create `AGENTS.override.md`. Codex reads it in place of `AGENTS.md`.

## AGENTS.md

`reference/templates.md` holds the template. It carries:

- The commands, including how to prepare a fresh worktree.
- Three pointers: read `HANDOFF.md` first if it exists, where the docs index is, and "a change to a user-facing surface updates its docs page in the same change".
- A rule table that pairs each rule with what enforces it: a lint, a test, a type, or "nothing yet". A rule with "nothing yet" is a candidate for a check, and the table makes that visible.
- The split: a note a stranger who cloned the repo could use goes in `AGENTS.md`; local paths, personal workflow and private context go in `AGENTS.local.md`.

Budget: keep the root `AGENTS.md` under 150 lines and 8 KB. Every harness loads the whole file into every session, so each line is paid for on every task. When an existing file is over budget, report it and propose moves; do not cut content yourself.

In a monorepo, give a package its own `AGENTS.md` when its commands or rules differ from the root, and list those files in the root `AGENTS.md`. A harness started in the package folder loads both files; a harness started at the root loads only the root file (`reference/harness-loading.md`).

## An existing instruction file

When the audit finds a `CLAUDE.md`, a mixed `AGENTS.md` or another instruction file with real content, migrate it. Do not write a template beside it or over it. Follow `reference/migration.md`.

## Reference

- `reference/templates.md`: every file this skill writes.
- `reference/migration.md`: moving an existing instruction file into the split, with the privacy scan.
- `reference/harness-loading.md`: what each harness was observed to load, with dates and versions, and how to probe again.
