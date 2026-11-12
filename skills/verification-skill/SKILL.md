---
name: verification-skill
description: Create, adopt or maintain a project's own verify-<app> skill, the scripted way for any agent to launch the real app, drive a feature the way a user does, and capture proof. Use for "create a verification skill", "make a control skill for this repo", "set up verification for this app", "move the verify skill", "maintain the verification skill", "audit the verify skill".
---

# verification-skill

A project needs one scripted way to prove behaviour in the running app: launch it, drive a feature the way a user would, capture evidence. This skill writes that as a skill inside the project, `verify-<app>`, and keeps it true as the app changes.

The reader of what you generate is an agent that has never seen the app and opens the skill in the middle of a task. It may run in a different harness from yours, and it may belong to an outside contributor who does not have this skill. So the generated skill is self-contained: it depends on no file outside the project.

Work on the current repo unless you are given a path. `<skill>` in a command is this skill's folder, the one that holds this file. `<verify>` is the generated skill's folder, `.agents/skills/verify-<app>`.

## Choose the mode

- **Create**: the repo has no verification skill.
- **Adopt**: the repo has one in a harness-specific folder, such as `.cursor/skills/verify-<app>/`, or one whose feature files lack a `Source` section. Read `reference/adopt.md`. Adopt comes before maintain.
- **Maintain**: the repo has an adopted one and it may have drifted from the app. Read `reference/maintain.md`.

Look before you choose: `find .agents/skills .claude/skills .cursor/skills -maxdepth 1 -name 'verify-*' 2>/dev/null`. A verification skill in this sense has a `features/` map. Leave any other `verify-*` skill alone (a model check, a lint). With several that have a map, ask which; with nobody to ask, handle each in turn.

## Create

1. Interview the repo. Answer these from the code and ask the user only what you cannot observe.
   - Surface: what a user touches (web UI, CLI, TUI, desktop app, API, library). Pick the main one and note the rest.
   - Run: how the app starts locally. Prefer the repo's own command (package scripts, Makefile, README quickstart). Note ports, environment variables, seed data and sign-in.
   - Drive: how a shell command can act on it. Use what the repo already has first: Playwright or Cypress specs, expect scripts, a PTY helper, endpoints that `curl` can reach, a debug port. Otherwise use a browser driver for web and Electron, plain invocation with captured output and exit code for a non-interactive CLI, tmux or a PTY for an interactive CLI or TUI, and plain HTTP for a service. Do not build on a harness's built-in browser or screen tool; the next agent may not have it.
   - Observe: what can be captured (screenshots, terminal text, response bodies, logs, exit codes, stored data).
   - Isolate: whether two instances can run side by side (ports, data folders, profiles). If they cannot, the generated skill says so and refuses to drive an instance it did not start.
2. Check that the checkout builds and starts. Point the app at a temporary data folder for this check; never let it write to the user's real data. If it does not start, fix that first or report exactly what fails. Do not generate against a broken base.
3. Write `<verify>/SKILL.md` and its helpers, following `reference/generated-skill.md`.
4. Seed the feature map in `<verify>/features/`, following `reference/feature-map.md`. With five or fewer user-facing features, map them all. With more, map the three to five that the README and the docs lead with, and list every other one in the index as not mapped yet, so that the gap is visible.
5. Make it findable, following `reference/placement.md`: the links for other harnesses, one line in `AGENTS.md`, and the ignore rule for run output.
6. Prove it. Run the generated skill's own instructions end to end: launch, doctor, drive every feature you mapped, capture evidence, clean up. Then check that the evidence is still at the path the skill names. Fix what fails and run the cleanup after every failed attempt. A generated skill that was never run is a draft; say so if you could not run it.
7. Report: the surface and the driver chosen, the features mapped and not mapped, which features have automated coverage, what the proof run covered, and anything that a stand-in hid (a container in place of a real machine, a local stack in place of a remote one).

## Rules for every mode

- Edit only `<verify>`, the links to it, the `AGENTS.md` line and the ignore rule. Never edit product code. Where the app does not do what the map says, decide which is wrong: a stale map is fixed, a broken app is reported.
- A recipe must be able to fail. For each check, ask what it would print if the feature were broken; a check that also matches the failure output proves nothing.
- Never weaken a recipe to make it pass. A feature that cannot be driven is recorded with the route you tried and the precondition that was missing.
- Proof standards, written into the generated skill (leave out one that has nothing to apply to in this app):
  - Drive the real user path, not an internal setter or a test-only endpoint.
  - Capture the action and the resulting state, not only the last screen.
  - Check the side effect (the file written, the row stored, the message sent) through a second, read-only view.
  - Mock only where production already isolates an external system.
  - When the safe path is a dry run or a test mode, observe what it skips; do not trust its name.
- Automated tests are recorded, not added. Each feature file lists the test that covers it, or "none". A test of a layer below the user path (a store function, not the command) counts as partial; say which layer. Where a cheap automated test would cover a feature, propose it in the report. Adding it is a separate change for the owner to approve.
- A feature map entry names source paths. This is how a later agent finds which features a diff touches, with `<verify>/scripts/features-for`.

## Reference

- `reference/generated-skill.md`: the sections of the generated `SKILL.md`, and the helper rules.
- `reference/feature-map.md`: the index and the feature file, with an example.
- `reference/placement.md`: where the skill goes so that each harness finds it, with the probe results.
- `reference/adopt.md`: moving an existing verification skill.
- `reference/maintain.md`: the upkeep pass.
- `NOTICE`: the source this skill is adapted from, and its licence.
