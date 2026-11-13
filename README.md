# agent-skills

Agent skills for Claude Code, Codex, Cursor, Pi and Grok, in one repo that every harness on your machine reads live.

[![CI](https://github.com/smarzban/agent-skills/actions/workflows/ci.yml/badge.svg)](https://github.com/smarzban/agent-skills/actions/workflows/ci.yml) [![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

## Skills

| Skill | Use it to |
|---|---|
| `handoff` | Write or pick up the per-worktree `HANDOFF.md` note, so a new session or another harness can carry on. |
| `repo-setup` | Give a repo its baseline: `AGENTS.md`, ignore rules, one verify command that CI also runs, a worktree setup script. |
| `writing-readmes` | Write, overhaul or review a README, with the quickstart proven by a cold run. |
| `writing-docs` | Write, audit or update user docs and maintainer docs from the source, as plain Markdown or for a docs framework. |
| `publishing-a-repo` | Check a private repo before it goes public. Runs only when asked by name. |
| `verification-skill` | Create, adopt or maintain a project's own `verify-<app>` skill: launch the real app, drive a feature the way a user does, capture proof. |
| `deliver` | Take one task from agreed to proven: state what done means, build, prove, review, open the pull request, then stop for you or merge when told to. |
| `correct` | Turn a mistake agents keep making in a repo into a check that fails: a design change, a type, a lint or a test, with a written rule last. |
| `coordinator` | Run several builder agents: plan tasks, brief each builder, review its pull request, test the result, then hand it over or merge it when you said to. Works in T3 Code, Herdr, Rex or a plain terminal. |
| `triage` | Check an issue or a pull request from outside: reproduce a bug, weigh a feature request, read what a diff really does. Ends with a verdict, a drafted reply, and a before and an after for you to test. |
| `shared-skills` | Add or edit a skill in this repo. |

`skills/_shared` holds the prose style and the scripts that several skills link to. It is not a skill.

Skills reach `skills/_shared` through relative symlinks, for example `skills/writing-docs/reference/style.md`. The links work when a harness reads this repo in place, which is what `bin/skills install` sets up. Copying a single skill folder out of the repo breaks them: copy `skills/_shared` beside it, or replace the links with the files they point to. The plugin routes below copy the whole repo and keep them.

## Quickstart

Needs `git`, `bash` and `python3` on the path, and a system with symlinks. Tested on macOS.

Install once per machine:

```sh
git clone https://github.com/smarzban/agent-skills.git ~/Workspace/agent-skills
~/Workspace/agent-skills/bin/skills install
```

`install` creates links, turns on the repo's git hooks, and adds a Claude Code `SessionStart` hook (a backup of `~/.claude/settings.json` is written next to it). It prints a line per link and per hook, then the status table. Each skill shows `linked` for Claude Code and Cursor, and the bundle line shows `(linked)  -> Codex, Pi, Grok`.

To try a skill, start a new agent session in a git repo and ask it to "write a handoff". The `handoff` skill writes `HANDOFF.md` for the current branch.

## How each harness gets the skills

| Harness | Route | Edit a skill | Add a skill |
|---|---|---|---|
| Codex, Pi, Grok | one folder link `~/.agents/skills/agent-skills` → `skills/` | live | live, no step |
| Claude Code, Cursor | one link per skill in `~/.claude/skills/` and `~/.cursor/skills/` | live | linked by `bin/skills new`, on commit, pull or checkout, and on Claude Code session start |

Claude Code does not scan nested skill folders, so it needs a link per skill. Cursor gets the same per-skill links because nested discovery there is not verified. `bin/skills link` makes them; the git hooks and the `SessionStart` hook run it for you.

## Daily use

Run these from the repo root.

```sh
cd ~/Workspace/agent-skills
bin/skills new my-skill   # scaffold skills/my-skill/SKILL.md and link it
bin/skills status         # what is linked, what conflicts
bin/skills link           # relink now, prune links to deleted skills
bin/skills update         # pull the newest skills and relink
bin/skills uninstall      # remove every link and hook it created
```

Use only `name` and `description` in frontmatter; both are honoured by all five harnesses. One skill also sets `disable-model-invocation: true`, which Claude Code and Pi honoured (the skill runs only when asked for) and Codex ignored when this repo was set up in November 2026; Cursor and Grok are untested. Its text therefore also tells the agent when not to run it.

The linker never overwrites a file, folder or link it did not create. `status` shows same-named skills that already exist in a harness folder so you can retire the old copy.

## Updating

```sh
~/Workspace/agent-skills/bin/skills update
```

It fetches `main`, fast-forwards and relinks, and prints `updated  <old> -> <new>` or `already up to date`. It changes nothing and exits 1 when the clone is not on `main`, has uncommitted changes, has commits of its own, or cannot reach GitHub. A session that is already open keeps the skills it loaded; the next one gets the new ones.

To update every day, schedule that command yourself, for example with cron: `0 9 * * * ~/Workspace/agent-skills/bin/skills update --quiet`. Whatever lands on `main` then becomes instructions your agents follow, without you reading it first.

## Install as a plugin

The clone above is the route that stays live and reaches all five harnesses. Three harnesses can also install the repo as a plugin, without `bin/skills`. A plugin is a cached copy that updates when the harness refreshes it, and its skills are named `agent-skills:<skill>`.

- Claude Code: `/plugin marketplace add smarzban/agent-skills` then `/plugin install agent-skills@smarzban`
- Grok: `grok plugin install smarzban/agent-skills --trust`
- Pi: `pi install git:github.com/smarzban/agent-skills`

Each of these was installed into an empty home folder in November 2026 and kept the files under `skills/_shared`. Loading the skills in a session from a plugin has not been tested. Install the whole repo: an installer that copies single skill folders drops the links to `skills/_shared`.

Use one route per harness: links or a plugin, not both, or each skill loads twice.

## Use with agents

Each skill is a folder under `skills/` with a `SKILL.md`, for example `skills/deliver/SKILL.md`. Agents working on this repo follow [`AGENTS.md`](AGENTS.md).

## Contributing

Read [`AGENTS.md`](AGENTS.md) for the commands and the rules a change must pass. To add or edit a skill, ask your agent to use the `shared-skills` skill.

## License

MIT. See [`LICENSE`](LICENSE).
