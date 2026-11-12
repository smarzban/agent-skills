# Templates

Fill every `<...>` with a fact from the repo. Delete a line whose fact does not exist. The instruction-file templates are fenced here and not kept as real files, so that no harness loads them while it works inside this skill's folder.

## AGENTS.md

````markdown
# AGENTS.md

## Commands

- Prepare a fresh worktree: `scripts/worktree-setup`
- Verify, the same command CI runs: `<scripts/verify, or the repo's own verify command>`
- Build: `<command>`
- Test: `<command>`
- Run one test: `<command>`

## Before you start

- If `HANDOFF.md` exists, read it before starting.
- If `AGENTS.local.md` exists, read it. It holds private notes for this checkout and git ignores it.
- Docs index: `<docs/README.md>`
- A change to a user-facing surface updates its docs page in the same change. If the `writing-docs` skill is installed, run it in update mode.

## Rules

| Rule | Enforced by |
|---|---|
| <rule> | <lint, test or type that fails when it is broken, or "nothing yet"> |

## Needs the owner

A change to one of these paths is never merged by an agent alone.

- `<path or folder/>`

## Where notes go

A note that a stranger who cloned this repo could use goes in this file. Local paths, personal workflow and private context go in `AGENTS.local.md`. Never put content in `AGENTS.override.md`.

@AGENTS.local.md
````

- Leave out the docs-index line until a docs index exists.
- **Needs the owner** lists the paths the owner names: migrations, billing, auth, release scripts. Ask for them; do not guess. With none, leave the heading and write `- none yet`. The `deliver` skill reads this list from the base branch before it merges anything, and treats CI configuration and the list itself as always on it.
- Start the rule table with rules the repo already enforces (the formatter, the linter, the type checker, the test suite). Add a "nothing yet" row only for a rule the owner states.
- The last line is an import. Claude Code expands it and skips it when the file is absent. Codex and Pi show it as plain text and rely on the "read it" line above. See `harness-loading.md`.
- In a monorepo, add under **Before you start**: `- Before you change files under <packages/app/>, read <packages/app/AGENTS.md>.`

A nested `AGENTS.md` holds only what differs from the root: that package's commands and rules. It does not repeat the root file.

## AGENTS.local.md

Create it only when there is private content to put in it. The ignore rule is enough until then.

```markdown
# AGENTS.local.md

Private notes for this checkout. Git ignores this file.

Review: <how a change is reviewed here, such as the command or skill to run>
Evidence: <where pictures and recordings of proof are sent, such as a chat channel>
Outside code: <where code from an outside pull request may be run, such as a named machine; leave out to never run it>
Tracker: <where tasks and briefs live>
Plan: <where a plan is written down, when it is not the tracker>
```

## CLAUDE.md

Do not create one. When the repo already has a `CLAUDE.md`, it becomes this single line after its content has moved (`migration.md`):

```markdown
@AGENTS.md
```

## .gitignore

Add these lines if they are missing. Append; never reorder or remove existing lines.

```gitignore
# Private agent notes and the per-worktree handoff note
AGENTS.local.md
HANDOFF.md
```

Then add the rules for the detected stack that are missing:

| Stack | Rules |
|---|---|
| Node | `node_modules/`, `dist/`, `.env`, `.env.local`, `*.log` |
| Python | `__pycache__/`, `*.pyc`, `.venv/`, `.pytest_cache/`, `dist/`, `*.egg-info/`, `.env` |
| Rust | `target/` |
| Go | `bin/`, `*.test`, `coverage.out` |
| Ruby | `.bundle/`, `vendor/bundle/`, `log/`, `tmp/`, `.env` |
| Java, Kotlin | `build/`, `target/`, `.gradle/`, `*.class` |
| Any | `.DS_Store` |

Before you add a rule, run `git ls-files -i -c --exclude='<rule>'`. If it prints tracked files, report them and leave the rule out.

## scripts/verify

Copy `templates/verify` from this skill to `scripts/verify` and replace the commented lines with one `step <command>` line per real check, in the order CI runs them. Make it executable. Run it; it must exit 0 on a clean checkout.

## scripts/worktree-setup

Copy `templates/worktree-setup` from this skill to `scripts/worktree-setup`. Set `LOCAL` to the ignored files this repo needs, and delete the dependency lines for stacks the repo does not use. Make it executable.

The script copies `AGENTS.local.md` and the env files from the main checkout. It copies and does not link, because Claude Code does not load an imported file that is a link to outside the folder (`harness-loading.md`). The cost: a private note written in one worktree stays there. Keep durable private notes in the main checkout's `AGENTS.local.md`, where every new worktree picks them up.

## CI workflow

For GitHub Actions, `.github/workflows/ci.yml`. For another CI system that the repo already uses, add one job there that runs the verify command.

```yaml
name: CI
on:
  push:
    branches: [<default branch>]
  pull_request:

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@<current major>
      - uses: <toolchain setup action>@<current major>
        with:
          <version key>: <version from the repo's toolchain file>
      - run: <dependency install command>
      - run: <verify command>
```

Look up each action's current major tag (`git ls-remote --tags https://github.com/actions/checkout`). Do not write a version from memory.

## Pull request template

`.github/pull_request_template.md`. Propose it; the owner confirms the headings.

```markdown
## What changed

## How it was verified
```

## Issue template

`.github/ISSUE_TEMPLATE/bug.md`. Propose it; the owner confirms the headings.

```markdown
---
name: Bug
about: Something does not work as documented
---

## What happened

## What you expected

## Steps to reproduce
```

## CODEOWNERS

Entries are an owner decision. With a single author, propose this line and ask:

```
* @<owner>
```

## .editorconfig

```editorconfig
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
```

## .gitattributes

```gitattributes
* text=auto eol=lf
```

## Secret-scanning baseline

1. Run `<skill>/scripts/scan-private --history` and show the owner every `secret` hit. A real secret in history is rotated by the owner; this skill does not rewrite history.
2. If the repo uses a scanner (gitleaks, trufflehog, detect-secrets), add its config file and record the accepted existing findings as its baseline, so that only new findings fail. Add the scan as a `step` in `scripts/verify`.
3. Hosted secret scanning and push protection are remote settings. Give the owner the command or the settings page; do not turn them on.
