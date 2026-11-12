# The feature map

The map is the project's maintained list of what a user can do and how to prove each thing. A proof that drives one convenient entry point is incomplete when the map lists others.

## Index: `features/README.md`

```markdown
# <App> verification map

## Baseline
- <How the app is launched for a recipe, and the state every recipe starts from: named seed data, or an empty data folder.>
- <The doctor command and what it must report.>
- Never drive an instance that this run did not start.

## Conventions
- Start every recipe from the baseline unless its preconditions say otherwise.
- Treat every command as literal.
- Return to the baseline after a recipe changes data. Leave the evidence.

## Proof
- <What a UI proof holds, what a CLI proof holds, what a proof of a stored change holds.>
- Report a path you could not reach with the command you tried and the precondition that was missing. Do not report it as verified through another path.

## Features
| Feature | Covers | Automated |
|---|---|---|
| [Create a note](create-note.md) | browser and CLI creation, cancel, persistence | partly |
| [Search notes](search.md) | toolbar, keyboard and CLI search | none |

## Not mapped yet
- Export to Markdown (`src/export/`)
- Settings page (`src/web/settings/`)

## Paths with no feature
- `README.md`, `AGENTS.md`, `.gitignore`, `docs/`, `tests/`, `.github/`
```

**Automated** is `full`, `partly` or `none`: whether tests that run without an agent cover the feature. It tells the owner which features CI protects when an outside contributor changes them, and which only an agent run can check.

**Not mapped yet** lists each user-facing feature you found and did not write a file for, with its source path in backticks. `features-for` prints `NOT-MAPPED` when a change touches one. Remove a line when its file is written.

**Paths with no feature** lists, in backticks, the paths whose change needs no drive: docs, tests, CI and repo config. `features-for` stays quiet about them. Tests are listed here and not under a feature's `Source`.

## Feature file

One file per feature. An H1 title, one paragraph on what the user can do, then these six H2 sections in this order.

1. `Sub-features`: a short id and one line for each behaviour.
2. `How to get to it (user POV)`: every entry point a user has.
3. `Driving it with <driver>`: `<driver>` is the helper's name or the tool's. The section starts with `Preconditions:`. Each bullet pairs a user action with the exact command and the result to observe. The last bullet is the proof to capture, followed by a restore bullet when the recipe changed data.
4. `Gotchas`: traps that waste or invalidate a run.
5. `Source`: the paths whose change means this feature must be driven again. One per bullet, in backticks, relative to the repo root. A folder ends with `/`.
6. `Automated coverage`: for each sub-feature, the test that covers it and the command that runs it, or `none`. A test of a layer below the user path is `partly`, with the layer named.

Sections 1 to 4 are written from the user's point of view: name user paths, stable handles, required state, commands and observable results, and keep implementation detail out. Sections 5 and 6 are the two places where the map points into the code.

```markdown
# Create a note

A user can save a titled note from the browser or the CLI, cancel a draft, and see the saved note in a second view.

## Sub-features

- `create-save` stores a title and a body.
- `create-cancel` discards an unfinished browser draft.
- `create-cli` creates the same note from the terminal.

## How to get to it (user POV)

- Choose the `New note` button in the toolbar.
- Press `n` while focus is outside a text field.
- Run `notes create --title <title> --body <body>`.

## Driving it with control-notes

Preconditions:

- `control-notes doctor` reports the run's own URL and data folder.
- No note is titled `Release checklist`.

- **Open the editor.** Run `control-notes browser click --role button --name "New note"`. A form named `Note editor` appears with focus in `Title`.
- **Save.** Run `control-notes browser fill --role textbox --name "Title" --value "Release checklist"`, then `control-notes browser click --role button --name "Save note"`. A status named `Note saved` appears.
- **Confirm it was stored.** Run `control-notes cli -- notes list --format json`. The output holds one note titled `Release checklist`.
- **Cancel a draft.** Open a new note, fill `Title` with `Discard me`, and run `control-notes browser click --role button --name "Cancel"`. The list has no `Discard me`.
- **CLI entry.** Run `control-notes cli -- notes create --title "CLI note" --body "From the terminal" --format json`. Exit code 0, and the output holds the new id.
- **Proof.** Run `control-notes browser screenshot --path .verify/$RUN_ID/create-note/list.png` and save the `notes list` output beside it. Both show `Release checklist` and `CLI note`.

## Gotchas

- Pressing `n` while a text field has focus types the letter.
- Titles are trimmed on save. Check the rendered title, not the input value.
- The saved status alone is not proof. Read the note back.

## Source

- `src/web/notes/editor/`
- `src/cli/create.rs`
- `src/store/notes.rs`

## Automated coverage

- `create-save`, `create-cli`: `tests/create_note.rs`, run with `cargo test create_note`.
- `create-cancel`: none.
```

## Rules

- If the seed data lacks a precondition that a recipe needs, the recipe reports a failure and the reason goes in `Gotchas`. Do not weaken the recipe.
- `Source` paths are wide enough to catch a change that can break the feature. Where the code allows it, keep them narrow enough not to fire on every commit: shared code that every feature uses (the app shell, the router) goes to the feature that would show the break first. Where several features live in one file, list that file under each of them.
- Take `Automated coverage` from the test files, not from their names: open the test and confirm that it exercises the sub-feature.
- Check the map with `<verify>/scripts/features-for --all`. It lists every feature and exits 0 when it prints no `NO-SOURCE` and no `DEAD` line.
