# The before and the after

The owner tests a change fastest by seeing the old behaviour and the new one side by side. Set both up and leave them ready.

## The two checkouts

```sh
git worktree add --detach ../<repo>-before <base sha>
git worktree add --detach ../<repo>-after <head sha>
```

- **Before** is the base commit the change was built on. **After** is the head of the fix, or the head of the contributor's pull request.
- Give each its own data folder, port and profile, by the `verify-<app>` skill's rule for running two instances. Neither touches the owner's real data.
- Seed both with the same data, the data the reproduction needs.
- If the app cannot run two instances at once, say so, and give one command that switches between them.
- For a command-line tool, nothing is left running: give the two commands.

## What you hand over

```markdown
### Test it yourself
| | Before | After |
|---|---|---|
| Commit | <base sha> | <head sha> |
| Folder | <path> | <path> |
| Open or run | <address or command> | <address or command> |

1. <A step the owner does in both.>
2. <What they see before, and what they see after.>

Stop both with: <command>. Remove with: <command>.
```

- Write the steps as the report's own steps, so that the owner sees what the reporter saw.
- Start both and check them yourself before you hand over: the before shows the problem, the after does not.
- List both under **To undo** in your note. They stay until the owner says they are finished; then stop them and remove the worktrees.
- When the head commit changes, rebuild the after and say so.
