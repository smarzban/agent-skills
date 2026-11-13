# The changelog

A reader scans the changelog to learn what is different for them. It is a list, not a guide.

## Template

Use this for a repo with no changelog yet. In a repo that has one, keep its heading style, its section names and its own rules, and apply the wording rules below to the lines you add.

```markdown
# Changelog

## Unreleased

## 1.4.0 - 2026-03-09

### Breaking

- Config files from 1.2 and older are refused. Run `app migrate` once ([migrating](docs/migrate.md)).

### Added

- `app export --csv` writes the current view to a file ([export](docs/export.md)). Thanks @login (#41).

### Changed

- Search matches whole words by default; `--loose` gives the old behaviour.

### Fixed

- The list no longer jumps to the top after a refresh. Thanks @login for the report (#38).
```

The sections come in this order and only when they have a line: Breaking, Added, Changed, Removed, Fixed. The newest version is on top.

## Wording

- One change is one line, 30 words at most.
- Say what is different for the user, in the present tense: what they can do now, what behaves differently, what no longer goes wrong.
- No steps, no examples, no reasons, no history of how the change was made. For detail, link to the docs page.
- Name the command, key, flag or setting the user would type or see, in backticks. No file paths from the source, no function names, no pull request titles.
- A breaking change says what breaks and the one thing to do about it.
- Leave out what a user cannot see: refactors, tests, CI, internal docs, dependency bumps with no visible effect.
- A fix to something added in this same release is not a line. Edit the line that added it.
- Several small changes to one feature are one line.

If a section runs past about ten lines, look for lines to join or drop before you add more.

## Thanks

Thank everyone outside the project whose work or report is in the release, on the line it belongs to.

| What they did | Write |
|---|---|
| Wrote the change | `Thanks @login (#41).` |
| Reported the bug | `Thanks @login for the report (#38).` |
| Asked for the feature | `Thanks @login for the request (#38).` |
| Two people | `Thanks @one (#41) and @two for the report (#38).` |

- The number is their pull request or their issue.
- Take the login from GitHub, never from a commit's name or email: the pull request's `author.login`, and for a report or a request the author of the issue the pull request closes (`closingIssuesReferences`, or the issue the body names). A wrong login notifies a stranger.
- Do not thank the owner, a member or collaborator of the repo, a bot (a login with `[bot]` or an `app/` author), or an AI named as co-author.
- A contributor's commits brought in through another pull request still count: thank the person who wrote them.
- Before the checkpoint, check each login once: `gh api users/<login> --jq .login` prints it back.
