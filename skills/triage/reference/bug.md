# A bug report

## Reproduce it

1. Write down what the report claims as three lines: what was done, what was expected, what happened. If one of the three is missing, try the most likely reading before you ask.
2. Note the version, the platform and the settings it names. Where they differ from what you can run, say so in the verdict.
3. Reproduce it the way a user would, in the running app, on a clean checkout of the base at its tip. With a `verify-<app>` skill, launch and drive through it, and use its rule for running without touching real data. A unit test that fails is support, not a reproduction.
4. Try the nearest variations: the same steps on the released version the reporter names, with default settings, and with the input they gave exactly.
5. Capture the evidence: the commands, the output, a screenshot or a recording, the commit.

## Verdicts

| Verdict | When |
|---|---|
| Reproduced | You saw the wrong behaviour yourself, and can show it again |
| Reproduced only on an older version | Name the commit that fixed it |
| Not reproduced | You followed the report and saw the right behaviour. List what you tried |
| Needs more information | A step, an input or a setting is missing and no reading of the report gives it |
| Not a bug | The code and the docs show the behaviour is meant. Quote both. If only the code shows it, the docs are wrong, and that is a finding |
| Duplicate | Link the earlier one |

## When it is reproduced

- Find the cause before you write the task: the file and line, and since when (`git log -S`, `git bisect` when it is cheap).
- Look for the same mistake nearby. List what you find; do not widen the fix.
- Rate it for the owner: who meets it, how often, what it costs them, whether there is a workaround. Data loss, a security hole or a crash on the main path goes at the top of the verdict.
- A security hole is never described in a public reply. Say so in the verdict and draft a short reply that asks the reporter to use the project's private channel.
- The task's first "done when" item is the reproduction turning into the right behaviour. Then follow `deliver`, ending stop.
