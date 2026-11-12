# The generated skill

`<verify>` is `.agents/skills/verify-<app>`. Write every command in the generated skill as it is typed from the repo root, with the full relative path to a helper: `.agents/skills/verify-<app>/scripts/<helper>`.

```
.agents/skills/verify-<app>/
  SKILL.md
  features/
    README.md        index and shared conventions
    <feature>.md     one file per user-facing feature
  scripts/
    features-for     copied from this skill; lists the features a diff touches
    <helper>         whatever drives the app, when the repo has no driver of its own
```

Copy `<skill>/scripts/features-for` into `<verify>/scripts/` and keep it executable. The generated skill must work in a clone that does not have this skill.

## Frontmatter

```markdown
---
name: verify-<app>
description: "Drive <app> (<what it is>) the way a user does, through <surfaces>. Use to prove a change to <the feature areas> in the running app, and after changing any file a feature in its map covers."
---
```

Without frontmatter no harness registers the skill. Keep the description in double quotes; a colon followed by a space breaks unquoted YAML (seen with Ruby's YAML parser on 2026-11-12). Do not set `disable-model-invocation`: agents must reach for this skill unprompted.

## Sections

Write each section from what the interview found. Leave no placeholder.

**Opening.** Two or three lines: what the app is, which surfaces this skill drives, and what it deliberately does not touch (the user's real data folder, a shared instance, production).

**Which features to drive.** How to pick the recipes for a change:

```sh
.agents/skills/verify-<app>/scripts/features-for
```

It prints `DRIVE <feature file>` for each mapped feature whose source changed, `NOT-MAPPED <feature>` when the change touches a feature the index lists as not mapped yet, and `UNMAPPED <path>` for a changed path that nothing in the map names. Tell the reader to drive every `DRIVE` feature, to write the feature file for a `NOT-MAPPED` feature they changed, and to treat an `UNMAPPED` path under the app's source as a feature that may need a map entry.

**Launch.** The exact command that starts the app for verification and how to tell that it is ready (a log line, a prompt, a page element). Launch reaches a usable state from an empty data folder, including migrations and seed data. Ready means usable: a health check can pass while setup is still running. For a short-lived CLI or TUI there is nothing to keep alive: build once, create one run folder, and reset its data between recipes.

Launch creates the run id and prints it. Every later command takes the run id as an argument, because a shell variable may not survive from one command to the next in the reader's harness.

**Doctor.** One read-only check that answers "is this instance worth driving?": the process is up, it is the right build, the port or data folder is the one this run created, sign-in is valid. The reader runs it before the first drive and whenever something looks off.

**Drive.** The recipe, with real selectors and commands from this repo. Prefer stable handles (accessible names, data attributes, prompt strings, route paths) to coordinates and tab order. Point to `features/README.md` and say that a feature is proven only by its own file's recipe.

**Evidence.** What to capture for each kind of proof and where it goes. State the proof standards from this skill's rules. Evidence goes under `.verify/<run-id>/` at the repo root, which git ignores. Never write evidence or run state inside the skill folder: it would be committed, or pile up beside the recipes.

**Cleanup.** How to stop what the run started. Where the run started a process, stop it by the id recorded at launch, never by name. Cleanup removes instances and scratch state and leaves the evidence.

**Keeping the map current.** Three lines for the next agent:

- A change that adds or alters a user-facing feature updates that feature's file in the same change, including its **Source** paths.
- A new proof recipe written for a task is added to the feature file, not left in the chat.
- For a full check of the map against the app, run the maintain pass of `verification-skill` if it is installed.

## Helpers

- A helper is executable, and the skill body shows how to call it. A helper the reader has to read to use is not finished.
- Use a language the repo already depends on, or POSIX shell or Python 3. Do not add a dependency to the product for the sake of a helper; if the helper needs a package, keep it in a virtual environment under `.verify/` and show the one command that creates it.
- A helper refuses to run against anything it did not create: it checks that the data folder is its own before it writes.
- Keep run state (ids, ports, process ids) out of the skill folder: under `.verify/<run-id>/`, or in a temporary folder outside the repo with owner-only permissions when it holds a secret (a cookie jar, a token).
