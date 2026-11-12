# Placement

A project skill is read from a different folder by each harness. Put the skill in one place and link the others to it.

## Where it goes

1. The real folder is `.agents/skills/verify-<app>/`.
2. Claude Code reads only `.claude/skills/`. Add a relative link:

   ```sh
   mkdir -p .claude/skills
   ln -s ../../.agents/skills/verify-<app> .claude/skills/verify-<app>
   ```

3. If the repo already has a `.cursor/` folder, add the same link under `.cursor/skills/`. Do not create `.cursor/` for this.
4. Commit the links. Git stores them as links. A checkout on Windows without link support gets a text file instead; note that in the report.
5. Add one line to the repo's `AGENTS.md`, next to the build and test commands, in the section that says what to read before starting, or at the end of the file, so that a harness that finds no skill folder still finds the skill:

   ```markdown
   - To prove a change in the running app, follow `.agents/skills/verify-<app>/SKILL.md`. `.agents/skills/verify-<app>/scripts/features-for` lists the features a diff touches.
   ```

   If the repo has no `AGENTS.md`, report that and do not create one here; `repo-setup` does that.
6. Add `.verify/` to `.gitignore`. Run output and evidence go there.

A link that already points at the skill is left as it is. Never overwrite any other existing file or link with one of these names; report it and stop.

## What each harness was observed to read

One run each on 2026-11-12, in a git repo that held one probe skill in each of `.agents/skills`, `.claude/skills`, `.cursor/skills`, `.codex/skills`, `.pi/skills` and `.grok/skills`. Each harness was asked to list the skills it had, with file tools blocked.

| Harness | Project folders it listed skills from |
|---|---|
| Claude Code 2.1.322 | `.claude/skills` only. A link from there into `.agents/skills` was listed too. |
| Codex 0.167.2 | `.agents/skills`, `.codex/skills` |
| Pi 1.5.2 | `.agents/skills`, `.pi/skills` |
| Grok 1.0.67 | untested: the probe folder was not trusted, and only user-level skills were listed |
| Cursor | untested |

A harness update can change any row. Probe again before you rely on one: put a skill with a unique name in each folder and ask the harness, with tools blocked, to list the skill names it has.
