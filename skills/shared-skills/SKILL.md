---
name: shared-skills
description: Add, edit or remove a skill in the user's shared agent-skills repo, the single source of skills for Claude Code, Codex, Cursor, Pi and Grok. Use when asked to "create a skill", "save this as a skill", "add this to my skills", "update the X skill" or "make this available to all my agents".
---

# shared-skills

The user keeps one skills repo that every agent harness on this machine reads. Put personal skills there, not in a harness-specific folder such as `~/.claude/skills` or `~/.codex/skills`.

## Find the repo

Resolve the link: `readlink ~/.agents/skills/agent-skills` prints `<repo>/skills`. The repo root is its parent.

## Add a skill

1. Run `<repo>/bin/skills new <name>` (lowercase letters, digits, hyphens). It creates `skills/<name>/SKILL.md` and links it.
2. Write the frontmatter with only `name` and `description`; those two fields work in every harness. The description decides when an agent loads the skill, so state what it does and the phrases that should trigger it.
3. Write the body as direct instructions. Put scripts and reference files next to `SKILL.md` and refer to them by relative path.
4. Keep the skill harness-neutral: name tools generically ("run the command", "read the file") instead of one harness's tool names.

## Edit or remove a skill

Edit the files in place; every harness reads them through a link, so the change is live. To remove a skill, delete its folder and run `<repo>/bin/skills link` to prune the link.

## Check

Run `<repo>/bin/skills status`. A `CONFLICT` or an entry under "same name elsewhere" means an older copy of that skill lives in a harness folder and may shadow the repo copy; tell the user instead of deleting it.

Commit only when the user asks.
