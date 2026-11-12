---
name: publishing-a-repo
description: Check a private repo before its owner makes it public, and report what to fix. Covers secrets in git history, private names and links, licences, security policy, CI exposure to forks, discovery metadata, and a quickstart run from a clean clone. Run only when asked by name, for example "publishing-a-repo", "prepare this repo to go public", "pre-publish check".
disable-model-invocation: true
---

# publishing-a-repo

Prepare a repo for publication and report. The owner changes the visibility. This skill never changes a remote setting and never rewrites history; where either is needed, it gives the exact commands for the owner to run.

Run it only when the user asks for it by name. Work on the current repo unless you are given a path.

Fixes inside the working tree (a removed internal link, a corrected README) are proposed in the report and made only after a yes.

## Checks

Run all seven. For each, record pass, fail or not checked, with the evidence.

### 1. Secrets in the full history

```sh
scripts/scan-private --history
```

Run it from this skill's folder against the repo. Also run a dedicated scanner when one is installed (`gitleaks detect`, `trufflehog git file://.`); the script's pattern list is short. Read every `secret` hit. A hit in an old commit counts: publishing exposes every commit on every branch and tag.

For a real secret, the report says: rotate it first, because a secret that was ever pushed must be treated as leaked. Then, if the owner wants it out of history, propose the commands and do not run them:

```sh
git filter-repo --invert-paths --path <file>          # or --replace-text <rules file>
git push --force --all && git push --force --tags
```

State what a rewrite costs: every commit id changes, and every clone and open pull request must be redone. Publishing a fresh repo from a clean export is the alternative.

### 2. Private references

The same scan lists `path`, `email`, `host`, `ticket` and `author` hits across code, docs, commit messages and commit authors. Sort each one:

- Internal hostnames, URLs, wiki and tracker links, ticket ids: fail unless the owner says the target is public.
- Names and emails of people, in files and in commit authorship: list them. Whether each person agreed to be public is the owner's call.
- Home-folder paths, machine names, internal codenames: fail.

Also read by eye what a pattern cannot catch: `AGENTS.md` and other instruction files, code comments, test fixtures, sample data. Check that `git check-ignore -q AGENTS.local.md HANDOFF.md` exits 0 and that neither file was ever committed (`git log --all --oneline -- AGENTS.local.md HANDOFF.md` prints nothing).

### 3. Ownership and third-party licences

- Ownership: ask who owns the code (the user, an employer, a client) and whether every author in the `author` list contributed under terms that allow publication. Record the answer; do not assume it.
- Dependencies: list each dependency's licence with the ecosystem's tool (`npm ls --all` with a licence checker, `cargo license`, `pip-licenses`, `go-licenses`) or from the lockfile and package metadata. Flag copyleft and unknown licences against the licence the owner intends.
- Copied code: search for vendored folders and for licence headers that differ from the repo's. Each needs its notice kept.

### 4. Licence, security policy, code of conduct

- `LICENSE`: must exist before publication. If it is missing, report it. Never choose one.
- `SECURITY.md`: must name a reporting channel that the owner confirmed in this session. Do not derive one from commit metadata.
- Code of conduct: report whether one exists. Adopting one is the owner's decision.

### 5. CI workflows

Read every workflow file.

- A workflow triggered by `pull_request_target`, or by `workflow_run` after a fork's pull request, that checks out or runs the pull request's code can hand secrets to a fork. Fail it.
- A `pull_request` job that needs a secret gets none from a fork and fails. List each such job.
- List every secret and variable the workflows name, so the owner can confirm each exists and is scoped.
- Self-hosted runners on a public repo run strangers' code. Fail them.
- Jobs that reach private things (an internal registry, a private submodule, a private action) fail in public. List each.
- Actions pinned to a branch name run whatever that branch becomes. Recommend a tag or a commit.

### 6. Discovery

Read `reference/discovery.md`. Check the repo description and topics, the package manifest's description and keywords, the README's first sentence, and `llms.txt` if a hosted docs site exists. Description and topics are remote settings: propose the values and the command.

### 7. Quickstart from a clean clone

Clone the repo into a temporary folder with no credentials for anything private, and follow the README quickstart exactly, by a fresh subagent that gets only the README where one is available.

```sh
git clone --no-hardlinks . "$TMPDIR/publish-check" && cd "$TMPDIR/publish-check"
```

Fail on any step that needs a private registry, a private submodule, an internal host, an unpublished package, or a file that git ignores. If the README has no quickstart, report that and point to `writing-readmes`.

## Report

1. A table: each check, its result, and the evidence.
2. Blockers, in the order to fix them: secrets, then private references, then licence and ownership.
3. Proposed in-repo fixes, for a yes.
4. Commands for the owner to run, including the last one, which this skill never runs:

   ```sh
   gh repo edit <owner>/<repo> --visibility public --accept-visibility-change-consequences
   ```

5. What was not checked, and why.
