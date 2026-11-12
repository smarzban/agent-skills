# Target: in-repo Markdown

The main reader is an agent that opens one page in the middle of a task. It cannot ask what you meant, and it may not open a second page.

## Layout

Follow the repo's existing docs layout. Without one:

```
docs/
  README.md        index, routed by task
  docs-map.txt     page-to-source map
  quickstart.md
  install.md
  configuration.md
  usage/<feature>.md
  architecture.md
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

`docs/README.md` routes by task, not by file name:

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
