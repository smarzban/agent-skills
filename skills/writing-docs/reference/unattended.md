# Unattended update

Use this when a scheduled job or a coordinator runs the update with nobody present. Run **Update for a diff** with these changes:

- With no docs map, the outcome is blocked: the docs need a first write with the owner present. Open nothing.
- If `<skill>/scripts/docs-scope` prints no `STALE` page and no `UNMAPPED` path, the outcome is clean. Stop, and open nothing.
- Put the page edits on the branch `docs/update` and open one pull request. If a pull request from that branch is still open, update it; do not open a second one.
- Do not write a new page. List each `UNMAPPED` path in the pull request description with the choice it needs: a new page, an existing `page` line, or an `exclude` line.
- When the code looks wrong and the page right, leave the page and describe the disagreement in the pull request description, with the evidence.
- Do not move `verified-at` while an `UNMAPPED` path or a disagreement is unresolved. A later run then reports it again.
- A blocked run with page edits still opens the pull request and states blocked first. With no edits it opens nothing.
- Never merge the pull request. Ask no questions; record what you could not decide in the description.

The description starts with the outcome, then lists the pages edited, the items left for the owner, and what was not checked.
