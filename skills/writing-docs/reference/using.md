# Scope: using

Docs for people who run, install or call the project. They never need to open the source.

## Pages

| Page | Kind | Holds |
|---|---|---|
| Index | reference | What the project is in one sentence, and a route by task to every page. |
| Quickstart | tutorial | The shortest path from nothing to a visible result, with the expected output of each step. |
| Install | how-to | Prerequisites and every supported install path. |
| Configuration | reference | Every setting: name, type, default, effect. Each default checked against the code that reads it. |
| One page per feature | how-to | The task the feature serves, the steps, a runnable example with its output, the limits and errors. |
| Troubleshooting | how-to | Only failures you reproduced or found in the issue tracker. |

Drop a page that has nothing true to say. An empty "authentication" page is worse than none.

## Ledger source by repo type

| Repo type | Enumerate from | A feature is |
|---|---|---|
| CLI | the command registrations, or `--help` of every command | a command or a group of flags that serve one task |
| Service or HTTP API | the route table | a resource or a workflow across routes |
| Library | the public exports (`index` re-exports, `__all__`, the manifest's `exports`) | a capability, plus the public reference |
| Application | the navigation and the feature modules | something a user does in the UI |
| Framework | the public API and the extension points | a guide-sized capability |
| Skills or plugin bundle | the manifest, or the skill folders | a skill or a command |
| Monorepo | each package, by its own type | per package |

Specs, ADRs, plans and pipeline reports are not features and not usage pages, wherever they sit. Exclude their folders in the map.

## By repo type

- **Library**: no deploy pages. Usage is task-oriented guides plus the public reference. State the supported language and runtime versions.
- **CLI**: a command reference generated from the help output, plus recipes that chain commands for real tasks.
- **Service**: install, configuration, authentication if any, the API by resource, and operations (backup, upgrade, logs). Trace operations sequences; do not run them.
- **Application**: pages are written for people who are not programmers. Name the UI elements exactly as the UI does.
- **Monorepo**: one index that maps the packages, then a folder per package.

## Examples

Every example on a usage page is run, not only the quickstart and install commands. Show the command and its real output. For an example that needs something this environment lacks (a paid service, a device), keep it only if you traced it against the code, and list it in the report as not run.

## Community files

Write these only for this scope and only from evidence.

- `CONTRIBUTING.md`: belongs to the changing scope; link to it.
- `SECURITY.md`: needs a reporting channel the owner confirmed. A channel taken from commit metadata counts as invented. Without confirmation, do not create the file; report it.
- `CHANGELOG.md`: only when a release process exists, and only entries that tags or releases support.
- Licence and code of conduct: report a missing one for a public repo. Never choose one.
