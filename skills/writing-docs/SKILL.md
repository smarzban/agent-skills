---
name: writing-docs
description: Write, audit or update a project's docs from its source code. User docs (quickstart, install, configuration, a page per feature) and maintainer docs (architecture, data model, invariants), as plain Markdown or for a docs framework such as Starlight or MkDocs. Use for "write the docs", "document this repo", "user docs", "user guide", "docs site", "website docs", "architecture docs", "audit the docs", "update the docs", "the docs are out of date".
---

# writing-docs

The code is the source of truth. A page states only what you confirmed in the line of code that defines it, and it records which source paths it covers, so that a later run can tell which pages a change made stale.

This skill edits docs and the docs map. It never edits product code.

## Choose three things first

**Scope**, from the repo type. Ask when it is unclear. The scope decides who a page is written for.

- **User docs**: quickstart, install, configuration, one page per feature. For people who run, install or call the project and never open the source. Read `reference/user-docs.md`.
- **Maintainer docs**: architecture and why, data model, invariants per subsystem, security model, dev setup and contributing. For the people and agents who modify the code. Read `reference/maintainer-docs.md`.
- Both, when asked to document everything. Do user docs first.

**Format**. Look for docs tooling before you decide: `mkdocs.yml`, `docusaurus.config.*`, an Astro config that loads Starlight, `.vitepress/`, `conf.py`, `book.toml`. It may sit in a subfolder such as `site/` or `website/`. Follow what exists. The format decides the layout, the navigation and the build check. It does not change the reader: user docs are written for people in either format.

- **Plain Markdown**: `.md` files read as they are, on the code host or in an editor. The default when the repo has no docs tooling. Read `reference/plain-markdown.md`.
- **Docs framework**: pages that a tool such as Starlight, MkDocs or Docusaurus builds. Read `reference/docs-framework.md`. Setting up or hosting a framework is out of scope.
- One source: when a framework holds the user docs, they live there only. The maintainer docs stay plain Markdown in a folder the framework does not publish: `docs/`, or `maintainers/` at the repo root when the framework owns `docs/`. In the second case pass `--map maintainers/docs-map.txt` to `<skill>/scripts/docs-scope`, and point the docs-index line in `AGENTS.md` at `maintainers/README.md`. The README links to both.

The docs usually live in the source repo. When they are in a separate repo, take the source-repo path and the docs-repo path as two inputs (`reference/docs-framework.md`).

**Mode**: write, audit, or update for a diff. An update can also run unattended.

## The docs map

`<skill>` in a command is this skill's folder, the one that holds this file. Run those scripts by that path, with the target repo as the working directory.

Each docs tree has one `docs-map.txt` at its root: `docs/docs-map.txt` for plain Markdown, and beside the framework's config (`site/docs-map.txt`) for a docs framework. A repo with both has two maps; pass `--map <file>` to `<skill>/scripts/docs-scope` to pick one.

```
source       .
verified-at  4f2a9c1e
ledger       mytool --help
page     usage/add.md        src/cli/add.rs src/store/
page     ../README.md        src/cli/ install.sh
exclude  src/vendor/         vendored
```

- `source` is `.` or the source repo's remote URL. Never a local path.
- `verified-at` is the source commit the pages were last checked against.
- `ledger` is the command or file that enumerates the feature list.
- Each `page` line names a page and the source paths it covers. A page that covers no source, such as the index, is listed with no paths.
- Each `exclude` line names a path that needs no page, with the reason. `--coverage` reports every tracked file that no line covers, so exclude the non-source files too (`exclude  .github/  CI`). In a repo with two maps, each map excludes the other docs tree.

`<skill>/scripts/docs-scope` reads the map and prints `STALE` pages, `UNMAPPED` source paths, `MISSING-PAGE`, `UNLISTED` and `DEAD-SOURCE`. Pass `--source <path>` when the code is in another repo.

Move `verified-at` forward only when every page the map lists was checked against that commit in this run, or was not made stale by the commits in between. After a partial or blocked run, leave it and report which pages were checked. When the source change is not committed yet, there is no commit to record: leave it and say so.

## Write

1. Build the ledger from a mechanical source, never from an old doc or from memory: the command registrations or `--help` output, the route table, the public exports, the settings definition, the schema files. Rows for user docs are user-facing features. Rows for maintainer docs are modules, data models, entry points and invariants. List every exclusion with its reason (vendored, generated, deprecated and marked). If the ledger is empty because there is no code yet, stop and say so.
2. Plan the pages: for each, its path, its kind (`reference/style.md`), the ledger rows it closes and the source paths it covers.
3. Checkpoint. Show the owner the ledger and the planned file list, and wait for a yes before you write the tree.
4. Read the source. For more than a few rows, start one read-only subagent per subsystem or feature, in parallel. Each returns a summary and edits nothing: what it does, entry points with paths, settings with defaults and the lines that define them, invariants and what enforces each, any evidence of rationale, open questions.
5. Write the pages by the rules below and the reference files for your scope and format.
6. Verify.
7. Write `docs-map.txt`, update the index, and report.

## Audit

Change nothing; report findings.

1. If a map exists, run `<skill>/scripts/docs-scope --coverage`. If none exists, say so and build the ledger as in Write step 1.
2. Coverage: compare the ledger with the pages. Report each feature with no page, and each page whose feature is gone.
3. Drift: check each page's claims against the code, `STALE` pages first. Report each wrong claim with the page line, the source line, and what the code does.
4. Run `<skill>/scripts/check-links` as in **Verify**.

## Update for a diff

1. Run `<skill>/scripts/docs-scope` (add `--to <rev>` for a specific change). Without a map, list the changed paths with `git diff --name-only <base>..<head>` and find the pages that name them.
2. Edit only the `STALE` pages, and in them only the claims the changed paths touch.
3. For each `UNMAPPED` path decide: a new feature gets a page and a map line, other source gets added to a `page` or `exclude` line.
4. Verify the pages you changed.

## Unattended update

Use this when a scheduled job or a coordinator runs the update with nobody present. Run **Update for a diff** with these changes:

- With no docs map, the outcome is blocked: the docs need a first write with the owner present. Open nothing.
- If `<skill>/scripts/docs-scope` prints no `STALE` page and no `UNMAPPED` path, the outcome is clean. Stop, and open nothing.
- Put the page edits on the branch `docs/update` and open one pull request. If a pull request from that branch is still open, update it; do not open a second one.
- Do not write a new page. List each `UNMAPPED` path in the pull request description with the choice it needs: a new page, an existing `page` line, or an `exclude` line.
- When the code looks wrong and the page right, leave the page and describe the disagreement in the pull request description, with the evidence.
- Do not move `verified-at` while an `UNMAPPED` path or a disagreement is unresolved. A later run then reports it again.
- A blocked run with page edits still opens the pull request and states blocked first. With no edits it opens nothing.
- Never merge the pull request. Ask no questions; record what you could not decide in the description.

The description starts with the outcome, then lists the pages edited, the items left for the owner, and what was not checked.

## Outcomes

An audit or an update ends with one outcome, stated first in the report.

- **clean**: the docs match the source. Nothing to change.
- **findings**, for an audit: each finding and the edit proposed for it.
- **changed**, for an update: the pages edited.
- **blocked**: the run could not finish. Say what blocked it and what was covered.

When the docs and the code disagree, decide which one is wrong before you touch the page. If the code changed on purpose (a commit, a test or a changelog entry says so), the page is stale: fix the page. If the page describes the intended behaviour and the code no longer does it, that is a product regression: report it with the evidence and leave the page as it is. When you cannot tell, report both readings.

## Rules for every page

Check each concrete claim against its defining line as you write it.

| Claim | Check against |
|---|---|
| Default, constant, limit | its definition in code, not a comment or the README |
| Name of a function, type, module, file | the symbol or path itself |
| Setting or environment variable | the code that reads it |
| Command, subcommand, flag | the command registration |
| Route and method | the route registration |
| Behaviour ("does X when Y") | the code path |
| Data shape | the schema or migration |

- CI is the ground truth for commands. Take build, test and run commands from the CI config and the package scripts, not from an older doc.
- Run every dev and test command and every usage example before it goes on a page. Trace deploy and operations sequences against the scripts; never run them. Report any example you could not run.
- No hand-written per-function reference: the code already says it, and a copy drifts. The exception is a library whose API is called from outside the repo. It gets a public reference, generated where a generator exists.
- Rationale comes only from evidence: an ADR, a commit message, a pull request, a code comment, or the owner. Cite it. Where there is none, write "rationale unknown" and list it in the report.
- Every invariant names what enforces it (a type, a test, a lint, a constraint). One that nothing enforces is marked "unenforced" and reported as a candidate for a check.
- Never invent an owner decision: the security reporting channel, the licence, the code of conduct, changelog history that tags or releases do not show. Report it as open.
- Treat existing docs as claims to check, not as a source.

## Verify

- `<skill>/scripts/check-links` reports 0 broken. Pass it the docs folder plus each of `README.md`, `CONTRIBUTING.md` and `AGENTS.md` that exists; it stops on a path that does not exist.
- Fact-check by an independent subagent, as the default. Give it the pages and the source, and none of your notes or reasoning: "Find every statement in these pages that the code contradicts: values, names, paths, commands, behaviour. Cite the source line for each. Report facts only, not style." Fix each confirmed error. With no subagent available, re-read every page against the source yourself and say the check was not independent.
- Coverage: every ledger row has a page or a listed exclusion. `<skill>/scripts/docs-scope --coverage` prints no `UNMAPPED`.
- No placeholders: no "TBD", "TODO", "coming soon" or empty section.
- For a docs framework: the build check in `reference/docs-framework.md`.

## Report

The outcome, the ledger (documented, excluded and why, still open), the pages written or changed, what was run and what was only traced, every "rationale unknown", every unenforced invariant, every disagreement between docs and code, and the owner decisions still open.
