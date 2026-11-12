# The pull request body and the report

## Body

The body is a one-minute briefing for a reviewer who has the diff. Short sentences, few identifiers. It also holds the contract and the evidence, so that an agent that resumes the work, or the independent check, needs nothing else.

Use these headings in this order. Drop one that has nothing to say, except **Done when** and **Verification**.

```markdown
## Why
<The problem and the approach, in one to three sentences.>

## What changed
- <One to three bullets. Name a symbol or a path only when it carries the change.>

## Scope
- Covers: <what>
- Leaves out: <what, and where it goes instead>

## Done when
| # | Result | Proved by | Proof | Step |
|---|---|---|---|---|
| 1 | <observable result> | agent | <where the evidence is, below> | 5 |
| 2 | <observable result> | owner | <the exact steps for the owner> | |

## Verification
Commit: <full sha the evidence ran on. When items ran on different commits, name the commit beside each.>

1. `<exact command or recipe>`
   ```
   <the lines of output that show the result>
   ```

## Could break
<Who or what this touches beyond the diff, and why that is safe. One or two sentences.>

## For the owner to decide
<A choice that is the owner's and that this change leaves open. Drop the heading when there is none.>

## Review
<Who or what reviewed it, on which commit, and the outcome. Or: skipped at the owner's request.>
<Each finding that was set aside, with the reason.>
```

- Title: what the change does, in the repo's own commit style.
- Do not write the ending or a merge grant in the body.
- Look for a pull request template in the repo first. If there is one, keep its headings and put these sections under the ones that match.
- Update **Verification** and its commit whenever the proof is run again.
- Features you drove because `features-for` printed them, and that no "done when" item covers, go under **Verification** as "also driven", with the result.
- If running the app can touch real data, state under **Verification** how to run it safely. The independent check reads only this body.

## Report

Send it at the end, for either ending. Lead with the result; the owner should be able to decide from this alone.

```markdown
**<Merged | Ready for you | Stopped>**: <one sentence>. <pull request URL>

**Done when**
| # | Result | Evidence | Step |
|---|---|---|---|

**Your steps**
<For stop: the exact commands or clicks for the items that are the owner's to prove, then how to merge. For send: nothing, or what to look at.>

**Decisions**
<What you decided that the owner did not, and why.>

**Not done**
<What the task asked for that is not in this pull request, and anything you found and left alone.>

**Risk**
<What could still be wrong, and how the owner would notice.>
```

Use **Ready for you** when the pull request is proven, reviewed and green and waits for the owner, including a send that the gate turned into a stop; the first line then says which gate condition was false. Use **Stopped** when the work ended before that.

For a stop, **Your steps** ends with the merge command pinned to the proven commit, `gh pr merge <n> --squash --match-head-commit <sha>`, using the method of the repo's earlier merges, or squash when there are none.
