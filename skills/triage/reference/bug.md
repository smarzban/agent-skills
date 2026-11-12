# A bug report

## Security

Decide this first, before you reproduce anything. A report is a security report when it says the app can be made to leak data or credentials, run code, skip a permission, or be taken down.

- The verdict's first line says **Security**, and asks the owner to hide or move the public issue if it gives details.
- You may reproduce it locally. Push nothing: no branch, no pull request, no evidence sent anywhere. A fix, if the owner asks for one, stays on a local branch until they say how it is published.
- The drafted reply gives no detail. It thanks the reporter and asks them to use the project's private channel (its security policy, or the owner directly).
- This holds whether or not you reproduced it.

## Reproduce it

1. Write down what the report claims as three lines: what was done, what was expected, what happened. If one is missing, try the most likely reading before you draft a request for it.
2. Note the version, the platform and the settings it names. Where you cannot run one of them, the verdict is "could not test", not "not reproduced".
3. Reproduce it the way a user would, in the running app, on a clean checkout of the base at its tip, in a data folder of its own. With a `verify-<app>` skill, launch and drive through it. A unit test that fails is support, not a reproduction.
4. Try the nearest variations: the released version the reporter names, default settings, and the input they describe, typed or built by you.
5. For a bug that comes and goes, run it ten times, or for as long as the owner allowed, and report the count.
6. Capture the evidence: your commands, the output, a screenshot or a recording, the commit.

## Verdicts

| Verdict | When |
|---|---|
| Reproduced | You saw the wrong behaviour yourself on the tip of the base, and can show it again |
| Already fixed | It happens on the version they name and not on the tip. Name the commit that fixed it |
| Not reproduced | You followed the report and saw the right behaviour. List what you tried |
| Could not test | It needs a platform, a version, an account or a service you do not have. Say which |
| Needs more information | A step, an input or a setting is missing and no reading of the report gives it |
| Not a bug | The code and the docs both show the behaviour is meant. Quote both. If only the code shows it, the verdict is "owner's call", and the docs gap is a finding |
| Duplicate | The earlier one is open or was declined. Link it; if this report adds a new argument, say so |

## When it is reproduced

- Find the cause before you write the task: the file and line, and since when (`git log -S`, `git bisect` when it is cheap).
- Look for the same mistake nearby. List what you find; do not widen the fix.
- Rate it for the owner: who meets it, how often, what it costs them, whether there is a workaround. Data loss or a crash on the main path goes in the verdict's flags.
- Write the task in your own words. Its first "done when" item is your reproduction turning into the right behaviour. Then follow `deliver`, ending stop.
