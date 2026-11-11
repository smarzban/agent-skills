# agent-skills

One repo of skills, read live by Claude Code, Codex, Cursor, Pi and Grok on this machine.

## Install (once per machine)

```sh
git clone <this repo> ~/Workspace/agent-skills
~/Workspace/agent-skills/bin/skills install
```

`install` creates links, turns on the repo's git hooks, and adds a Claude Code `SessionStart` hook (a backup of `~/.claude/settings.json` is written next to it).

## How each harness gets the skills

| Harness | Route | Edit a skill | Add a skill |
|---|---|---|---|
| Codex, Pi, Grok | one folder link `~/.agents/skills/agent-skills` → `skills/` | live | live, no step |
| Claude Code, Cursor | one link per skill in `~/.claude/skills/` and `~/.cursor/skills/` | live | linked by `bin/skills new`, on commit, pull or checkout, and on Claude Code session start |

Claude Code does not scan nested skill folders, so it needs a link per skill. Cursor gets the same per-skill links because nested discovery there is not verified. `bin/skills link` makes them; the git hooks and the `SessionStart` hook run it for you.

## Daily use

```sh
bin/skills new my-skill   # scaffold skills/my-skill/SKILL.md and link it
bin/skills status         # what is linked, what conflicts
bin/skills link           # relink now, prune links to deleted skills
bin/skills uninstall      # remove every link and hook it created
```

Use only `name` and `description` in frontmatter; both are honoured by all five harnesses.

The linker never overwrites a file, folder or link it did not create. `status` shows same-named skills that already exist in a harness folder so you can retire the old copy.

## Other machines

Clone and run `bin/skills install`, then `git pull` to update. That is the route that stays live.

The repo also carries plugin manifests (`.claude-plugin/` for Claude Code and Grok, `package.json` for Pi). Plugin installs are cached copies that update only when the harness refreshes them, and these routes have not been install-tested:

- Claude Code: `/plugin marketplace add <owner>/<repo>` then `/plugin install agent-skills@agent-skills`
- Grok: `grok plugin install <owner>/<repo> --trust`
- Pi: `pi install git:github.com/<owner>/<repo>`

Use one route per harness: links or a plugin, not both, or each skill loads twice.
