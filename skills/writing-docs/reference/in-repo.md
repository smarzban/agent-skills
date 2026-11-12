# Location: Markdown in the repo

Plain Markdown in a docs folder, read on the code host, in an editor, or by an agent. This file covers the layout, the index and the checks. Who a page is written for comes from its scope (`user-docs.md`, `maintainer-docs.md`).

## Layout

Follow the repo's existing docs layout. Without one:

```
docs/
  README.md        index, routed by task
  docs-map.txt     page-to-source map
  quickstart.md          user docs
  install.md
  configuration.md
  usage/<feature>.md
  architecture.md        maintainer docs
  data-model.md
  internals/<subsystem>.md
  security-model.md
  development.md
```

If `docs/` already holds something else (specs, a site), put the tree in a named subfolder and exclude the rest in the map.

## Pages

- Self-contained. State the fact on the page, then link for depth. A page that only says "see the other page" fails its reader.
- Exact. Full commands in copyable blocks, real paths from the repo root, the expected output after a command whose result matters.
- Small. One task or one subsystem per file, so that an agent loads only what it needs.
- Stable headings. Other pages and `AGENTS.md` link to them.

## Index

`docs/README.md` routes by task, not by file name. When the folder holds both scopes, give the index two tables: one for using the project and one for changing it.

```markdown
| To do this | Read |
|---|---|
| Add a command | [internals/cli.md](internals/cli.md) |
| Change the task schema | [data-model.md](data-model.md) |
| Run the tests | [development.md](development.md) |
```

`AGENTS.md` points to this index in one line. If it does not, add the line `Docs index: docs/README.md` under its pointers and report that you did. Do not copy docs content into `AGENTS.md`.

## Check

```sh
scripts/check-links docs README.md CONTRIBUTING.md AGENTS.md
scripts/docs-scope --coverage
```

Both exit 0.
