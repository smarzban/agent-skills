# Create

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
6. Prove it. Run the generated skill's own instructions end to end: launch, doctor, drive every feature you mapped, capture evidence, clean up. Then check that the evidence is still at the path the skill names, and that `<verify>/scripts/features-for --all` exits 0. Fix what fails and run the cleanup after every failed attempt. A generated skill that was never run is a draft; say so if you could not run it.
7. Report: the surface and the driver chosen, the features mapped and not mapped, which features have automated coverage, what the proof run covered, and anything that a stand-in hid (a container in place of a real machine, a local stack in place of a remote one).
