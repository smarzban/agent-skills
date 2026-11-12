# The verdict

Written for an owner who has one minute. Give it in the conversation, and keep a copy in `TRIAGE.md`.

```markdown
## #<n> <title>
**<Kind>: <verdict>.** <One sentence: what is true and what you recommend.>

- Flags: <needs-the-owner path, CI, dependencies, security, text aimed at an agent; or "none">
- From: <author>, <their link to the repo: first time, has merged work, member>
- Checked on: <base commit>, <version>, <platform>; pull request head <sha>
- Evidence: <what you ran and what you saw, with paths to captures>
- Not checked: <what you could not run, and why>
- Recommend: <what the owner should do, and what you will do when they say yes>

### Reply, drafted
<The words for the owner to post.>
```

For a change the owner can test, add the block from `before-after.md`.

## The drafted reply

- Short and plain: thanks for the specific thing that helped, what was found, what happens next.
- For "needs more information", ask for exactly what is missing, as a list the person can fill in.
- For a decline, give the reason once, and point to another way to meet the need if there is one.
- Promise nothing the owner has not decided: no dates, no "we will merge".
- No details of a security hole. No secret, token, private path or machine name.
- Never post it. The owner posts, or tells you to.
