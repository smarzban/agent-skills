# Maintain

A feature map goes stale when the app changes. This pass checks every feature from the source and in the running app, and ships the corrections it proved. The unit is the feature: cover each one from source and drive each one live, without re-proving every sentence.

## Outcomes

End with one, stated first in the report.

- **clean**: every feature was read from source, every reachable feature was driven live, and nothing needs to change. No branch, no pull request.
- **changed**: one pull request with the corrections that were proven. Never merge it.
- **blocked**: the pass could not finish, or a proven fix could not ship safely. Say what blocked it and what was covered.

A feature whose stated precondition is not available here (production only, Docker not running) is reported as unreachable. It does not make the pass blocked. A feature that should be reachable here and could not be driven does.

## The pass

1. Find the skill: a `.agents/skills/verify-*/` folder with a `features/` map. Leave other `verify-*` skills alone. Several with a map: ask which, or run the pass on each when nobody is present. None: stop and offer the create mode. One in a harness-specific folder, or without `Source` sections: run the adopt mode first.
2. Check the index. Run `<verify>/scripts/features-for --all`.
   - Exit 0 means the index is sound.
   - A `DEAD` path: the code moved. Find where and correct `Source`.
   - A `NO-SOURCE` file: add the section.
   - Compare the index table with the feature files, and fix a missing, extra or duplicate entry.
3. Read the source, one read-only subagent per feature file, in parallel. Each answers "how does this feature work for a user?" from the code and returns: a summary, the entry points with paths, likely drift from the feature file with the line that shows it, whether the tests named under `Automated coverage` still exist and still exercise the sub-features, any recipe check that would also pass against a broken feature, and what to watch for when driving. A subagent never drives the app and never edits a file. Without subagents, do the same reading yourself, feature by feature. You may start driving (step 5) while the subagents read.
4. Reconcile. Every feature file has a summary. Merge recipes that can share an app state. Spot-check the drift that was reported. Then look for features the map lacks: read the commits since the recipes last changed (`git log --oneline <commit>..HEAD -- <the folders named in the Source sections>`, where `<commit>` is from `git log -1 --format=%h -- <verify>/features`) and the **Not mapped yet** list. Call a feature missing only when you can name its source path.
5. Drive every feature live: each sub-feature in each feature file, once. This is required even when the source reading found nothing. You do all the driving yourself, by the skill's own launch model. Follow the recipes as written; if you need an extra wait or step to get through, that is drift in the recipe. Hold three rules for the whole pass:
   - Never drive an instance you have not checked since it last surprised you. Run doctor before the first drive, on each fresh session, and after any failed drive. Where doctor cannot see the fault (a stuck screen on a healthy process), reset to a known state or relaunch.
   - Evidence captured so far survives every cleanup. Check it at its path; do not assume.
   - Nothing a drive started outlives its use. Clean up after a failed attempt as well as a good one.

   A doctor failure caused by a stale skill is drift: fix it and retry once before you call the pass blocked. A feature you cannot reach is recorded as unreachable with the route you tried and the precondition that was missing (sign-in, a licence, an operating system, outside state). If the map does not state that precondition, that is drift.
6. Triage each finding.
   - The map describes the feature wrongly or incompletely: fix the feature file.
   - A recipe check that also passes against a broken feature: tighten it, and drive it again.
   - The feature works and the helper cannot drive it: fix the helper, then drive it again before it ships.
   - `Source` or `Automated coverage` is wrong: correct it. Reading the file it names is the proof; no drive is needed.
   - Drift you suspect from the source and could not prove, because the feature is unreachable here: leave the file, and report it as suspected.
   - The app is broken: record it for the owner with the evidence. Keep it out of the pull request.
7. Ship or stop. For changed: re-read every file you edited, then open one pull request on the branch `verify/maintain`, based on the default branch, or on the adopt branch while that is unmerged. If a pull request from that branch is still open, update it. For clean or blocked: no pull request.

## Report

The outcome; each feature with source read, driven live or unreachable; the drift fixed; the drift suspected and not proven; the features proposed for mapping; the app faults found, with evidence paths, or "none"; and the sub-features whose automated coverage is `none`, as candidates for a test.

## Unattended

When a scheduled job or a coordinator runs the pass with nobody present: ask no questions, record what you could not decide in the pull request description, and never merge. A fault in the app is reported in the pull request description when there is one, and otherwise in the final message of the run.
