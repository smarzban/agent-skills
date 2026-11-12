# AGENTS.md

## Commands

- Prepare a fresh worktree: `scripts/worktree-setup`
- Verify, the same command CI runs: `scripts/verify`
- Check the skills only: `skills/_shared/scripts/check-skills`

## Before you start

- If `HANDOFF.md` exists, read it before starting.
- If `AGENTS.local.md` exists, read it. It holds private notes for this checkout and git ignores it.
- A change to a user-facing surface updates its docs page in the same change. If the `writing-docs` skill is installed, run it in update mode.

## Rules

| Rule | Enforced by |
|---|---|
| Each skill passes the structural checks in `check-skills`: frontmatter that parses as YAML (needs `ruby` on PATH), its keys, `name` matching its folder, a description, some referenced paths, links into `_shared`, and script headers. It does not check every reference; read the script for the exact rules. | `skills/_shared/scripts/check-skills` |
| `bin/skills`, `.githooks/*`, `scripts/verify` and `scripts/worktree-setup` pass shell lint (needs `shellcheck` on PATH) | `shellcheck -x`, in `scripts/verify` |
| Relative links, anchors and code-span paths in `README.md` and `AGENTS.md` resolve. `skills/` is not link-checked: skill docs name files in the repo a skill works on. | `skills/_shared/scripts/check-links`, in `scripts/verify` |

## Needs the owner

A change to one of these paths is never merged by an agent alone.

- `bin/skills`
- `.githooks/`

## Where notes go

A note that a stranger who cloned this repo could use goes in this file. Local paths, personal workflow and private context go in `AGENTS.local.md`. Never put content in `AGENTS.override.md`.

@AGENTS.local.md
