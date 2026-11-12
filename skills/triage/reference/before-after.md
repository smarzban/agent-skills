# The before and the after

The owner tests a change fastest by seeing the old behaviour and the new one side by side. Set both up and leave them ready.

## The two checkouts

```sh
git fetch origin
git worktree add --detach ../<repo>-triage-<n>-before origin/<base>
git worktree add --detach ../<repo>-triage-<n>-after origin/<base>
git -C ../<repo>-triage-<n>-after merge --no-ff --no-edit <head sha>
```

- **Before** is the tip of the base, where you checked the claim. **After** is that tip with the change merged in, which is what a merge today would produce. The merge commit is local and is never pushed.
- If the merge conflicts, abort it (`git merge --abort`), report the conflict as a finding, and build the after from the head commit alone, saying that it is not what a merge would give.
- `<head sha>` is a commit you have read: your own fix, or the pull request head by `pull-request.md`.
- Give each its own data folder, port and profile, and seed both with the same data, the data the reproduction needs. Neither touches the owner's real data.
- If the app cannot run two instances at once (the `verify-<app>` skill says when it cannot), give one command that switches between them.
- For a command-line tool, nothing is left running: give the two commands.
- In a sweep, set these up only for the items the owner picked.

## What you hand over

```markdown
### Test it yourself
| | Before | After |
|---|---|---|
| Commit | <base sha> | <base sha> + <head sha> |
| Folder | <path> | <path> |
| Open or run | <address or command> | <address or command> |

1. <A step the owner does in both.>
2. <What they see before, and what they see after.>

Stop both with: <command>. Remove with: <command>.
```

- Write the steps in your own words, following what the reporter did, so that the owner sees what they saw.
- Start both and check them yourself before you hand over: the before shows the problem, the after does not.
- List both under **Left running** in `TRIAGE.md`. They stay until the owner says they are finished; then stop them and remove the worktrees.
