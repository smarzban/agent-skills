---
name: writing-readmes
description: Write, overhaul or review a repo's front-door README.md. The first screen says what the project is and who it is for, the quickstart is proven by a cold run, and reference material moves to docs. Use for "write the README", "fix the README", "review the README", "the readme isn't clear", or a README that has grown into a docs dump.
---

# writing-readmes

A README is the front door. On the first screen a newcomer learns what the project is, whether it is for them, and how to start. Everything deeper lives in the docs and is linked.

Work on the current repo unless you are given a path. In a monorepo, "the README" is the root README; write a package README only when asked for it. Follow `reference/style.md` for the prose.

In Write mode, when you are in the repo's main checkout (`git rev-parse --path-format=absolute --git-dir --git-common-dir` prints the same path twice), do not edit there: another session may be working in it. Do the whole run on a new branch in a worktree of your own (from the repo root, `git worktree add -b readme ../<repo>-readme`; if that branch or folder exists, pick another name, never reuse or delete it) and give its path and branch in the report. Work in place when the repo has no commits or the README has uncommitted changes.

## Modes

- **Write**: produce or overhaul the README. This is the default.
- **Review**: report only, change nothing. Use it when asked to review, check or audit a README. Run steps 1 and 4 against the existing file and list each finding with its line and the evidence.

## Steps

`<skill>` in a command is this skill's folder, the one that holds this file. Run those scripts by that path, with the target repo as the working directory.

1. Read the repo before the README: the package manifest, the entry points, the CI config, the docs folder, the licence file. Settle the repo type (library, CLI, service, application, framework, monorepo, skills bundle) and the reader. Then read the existing README and mark each claim true, stale or unverifiable against the code.
2. Choose the sections from `reference/anatomy.md` for that repo type. Leave out a section that has nothing true to say.
3. Write.
   - First screen: the name, one sentence that says what it is and who it is for, then three to six highlights. Each highlight is a fact a reader can check (a command, a supported platform, a limit, a measured number), not a claim about quality.
   - Quickstart: the shortest path from nothing to a visible result, in copyable blocks with the expected output. Keep the full install (other platforms, options, configuration) in its own section or in the docs.
   - Move reference material (the full configuration table, every command, deployment) to the docs and leave a two-line summary with a link. Move it; do not delete it. If no docs page exists for it, create one or keep the material and report it.
   - Contributing: link to `AGENTS.md` and `CONTRIBUTING.md` for the commands and rules. Do not repeat them.
   - For a project that agents consume (a CLI, an MCP server, a library, a skills bundle), add the **Use with agents** section from `reference/anatomy.md`.
4. Verify.
   - Quickstart: give a fresh subagent only the README and a clean checkout (`git clone` into a temporary folder, or `git worktree add`), and tell it to follow the quickstart exactly and report each command, its output, and where it had to guess. A guess is a defect in the README. Without a subagent, run the quickstart yourself in a clean checkout and say that the cold run was not independent.
   - Every other command, flag, default and path: open the code that defines it.
   - Links and paths: run `<skill>/scripts/check-links README.md`. It must report 0 broken.
   - Badges: each badge points at something that exists. The workflow file named in a CI badge is in `.github/workflows/`, the package named in a version badge is published, and the licence badge matches the `LICENSE` file. Remove a badge that fails this.
   - Images: every image path resolves. Never link an image that is not in the repo; list it as something for the owner to supply.
5. Report: what the README now covers, what moved to which docs page, what you could not verify, and the owner decisions.

## Owner decisions

- Licence. For a public repo, or one about to become public, a missing `LICENSE` file is reported, and the README gets no licence section until the owner picks one. Never choose a licence. For a private repo, do not raise it.
- Screenshot or demo. Propose where one would help. Do not fabricate one.
- If the repo is public, check that its description and topics say the same thing as the first sentence. `publishing-a-repo` covers discovery in full.
- `llms.txt` belongs to a hosted docs site, not to a README. Do not add one to a repo without a site.
