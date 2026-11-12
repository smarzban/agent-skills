# README anatomy

Sections in the order readers expect. Keep the order, name the headings in the repo's own words, and drop what does not apply.

| # | Section | Keep when | Holds |
|---|---|---|---|
| 1 | Title | always | The project name, matching the repo or package. |
| 2 | One sentence | always | What it is and who it is for, under 120 characters, directly under the title. Name the category in plain terms ("CLI", "MCP server", "ORM"). |
| 3 | Badges | they point at something real | CI status, version, licence. No tracking pixels. |
| 4 | Visual | the project has a UI and an image is in the repo | A screenshot or a short recording. |
| 5 | Highlights | always | Three to six checkable facts. |
| 6 | Contents | the page is longer than about 100 lines | Links to the sections. |
| 7 | Quickstart | always | The shortest path to a visible result, with expected output. |
| 8 | Install | libraries and tools | Prerequisites and every supported install path, or a link to the docs page. |
| 9 | Usage | libraries and tools | A few runnable examples and the most-used options; a link to the full reference. |
| 10 | Use with agents | agents consume the project | See below. |
| 11 | Documentation | deeper docs exist | Links into the docs, routed by task. |
| 12 | Status | the project is pre-release, slowed or archived | One or two lines. |
| 13 | Contributing | contributions are welcome | A link to `AGENTS.md` and `CONTRIBUTING.md`. |
| 14 | Licence | a `LICENSE` file exists | The licence name and a link to the file. Last section. |

## Inline or link

Inline what most readers need on day one: the sentence, the highlights, the quickstart, the handful of most-used commands or settings, one small example. Link everything else: the full configuration reference, every command, deployment, architecture. A section that reads like reference material gets a two-line summary and a link to its docs page.

## Use with agents

For a project that an agent runs, calls or loads. Each line is a fact the agent's operator can act on:

- **CLI**: the command that prints machine-readable output (`--json`), the exit codes, and whether any command waits for input.
- **MCP server**: the exact server entry to add to a client's configuration, the transport, and the tools it exposes.
- **Library**: the import line and the one call that shows the main use, with types.
- **Skills bundle**: the install command per harness and the name of each skill.
- Any of them: where the agent-facing instructions live (`AGENTS.md`, a skill file) and the docs index path.

## By repo type

- **Library**: 1, 2, 3, 5, 7 (install, import, first call), 9, 10, 11, 13, 14. State the supported language and runtime versions.
- **CLI**: 1, 2, 3, 4, 5, 7 (install and the one command that shows value), 8, 9, 10, 11, 13, 14.
- **Service or HTTP API**: 1, 2, 3, 5, 7 (run locally and make one request), 8, 9 (the common settings), 11, 13, 14. Deployment and operations are linked.
- **Application**: 1, 2, 3, 4, 5, 7, 8, 11 (routed: using it, running it, changing it), 13, 14.
- **Framework**: 1, 2, 3, 5, 7 (scaffold and first app), 11 (guides, concepts, reference), 13, 14.
- **Monorepo**: the root README maps the packages and how they relate, with one quickstart for the whole. Each package has its own README of the matching type.
- **Skills bundle**: 1, 2, 5 (the list of skills, one line each), 7 (install into one harness and trigger one skill), 10, 13, 14.
