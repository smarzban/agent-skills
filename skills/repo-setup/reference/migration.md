# Migrating an existing instruction file

Use this when the repo already holds real instructions in a file that does not fit the split: a populated `CLAUDE.md`, an `AGENTS.md` that mixes public and private notes, a `.cursorrules`. Migration moves lines. It does not write, reword or delete them; improving the text is a separate task for the owner to ask for.

## Procedure

1. Save the original outside the repo, so that the check in step 6 has something to compare against.

   ```sh
   cp CLAUDE.md "$TMPDIR/original-instructions.md"
   ```

2. Read the whole file. Sort each line with one question: could a stranger who cloned this repo use it? Yes: it is public and goes to `AGENTS.md`. No (local paths, personal workflow, machine names, private context): it goes to `AGENTS.local.md`. When unsure, treat it as private.
3. Write the public lines into `AGENTS.md`, unchanged, under the template's headings or under their own headings below the template. Write the private lines into `AGENTS.local.md`, unchanged.
4. Add the template parts that the original lacked: the commands, the pointers, the rule table. Rules the original states go into the rule table with their enforcer, and stay in place as well.
5. Run the privacy scan on the public half before anything is committed.

   ```sh
   scripts/scan-private AGENTS.md
   ```

   Read every hit. Move each real one to `AGENTS.local.md`. Report the scan result to the owner, including a clean one.
6. Prove that nothing was lost.

   ```sh
   scripts/lines-kept "$TMPDIR/original-instructions.md" AGENTS.md AGENTS.local.md
   ```

   It must report every line kept. Fix any `LOST` line before you go on.
7. Only then replace the content of `CLAUDE.md` with the single line `@AGENTS.md`. Show this as a diff and get a yes. Claude Code ignores `AGENTS.md` while a `CLAUDE.md` without that import exists (`harness-loading.md`). Leave other tool-specific files (`.cursorrules`, `GEMINI.md`) in place and report them; whether to retire them is the owner's call.
8. If the migrated `AGENTS.md` is over the size budget, report the size and propose what could move to a nested file or to the docs. Do not cut it.

## When a private file becomes public

Run `git check-ignore -q CLAUDE.md`. If the original was ignored or untracked, its content has never been public, and `AGENTS.md` is a tracked file. Say so in the proposal, in these terms: this content becomes readable by everyone with access to the repo once it is committed. Get an explicit yes for that, separate from the rest.

If the original was tracked, its private lines stay in the git history after they move. Say so. Removing them means rewriting history, which is the owner's decision and not part of this skill.

## Mirrors

Ask once: is this file mirrored anywhere that has to stay in step, such as a wiki page or a shared document? The repo cannot tell you. If it is, list the mirror as a follow-up for the owner. Do not edit it.
