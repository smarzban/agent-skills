# AGENTS.md

## Commands

- Prepare a fresh worktree: `scripts/worktree-setup`
- Verify, the same command CI runs: `scripts/verify`
- Check the skills (frontmatter and referenced files): `skills/_shared/scripts/check-skills`

## Before you start

- If `HANDOFF.md` exists, read it before starting.
- If `AGENTS.local.md` exists, read it. It holds private notes for this checkout and git ignores it.
- A change to a user-facing surface updates its docs page in the same change. If the `writing-docs` skill is installed, run it in update mode.

## Rules

| Rule | Enforced by |
|---|---|
| Each skill's frontmatter has only `name` (matching its folder) and `description`, and its referenced files exist. YAML syntax is not parsed. | `skills/_shared/scripts/check-skills` |
| `bin/skills`, `.githooks/` and `scripts/` pass shell lint (needs `shellcheck` on PATH) | `shellcheck -x`, in `scripts/verify` |

## Needs the owner

A change to one of these paths is never merged by an agent alone.

- `bin/skills`
- `.githooks/`

## Where notes go

A note that a stranger who cloned this repo could use goes in this file. Local paths, personal workflow and private context go in `AGENTS.local.md`. Never put content in `AGENTS.override.md`.

@AGENTS.local.md
