# A feature request

You do not decide whether the project wants a feature. You give the owner what they need to decide quickly.

## Check

1. **The need behind it.** State the problem the person has, apart from the solution they propose. A good request often has a smaller answer.
2. **Whether it exists.** Search the code, the docs and the settings. A feature that exists and was not found is a docs finding, and the reply shows how to use it.
3. **Whether it was asked before.** Link earlier issues and what was decided.
4. **Whether it fits.** Read what the project says it is for and what it says it is not: the README, the docs, a roadmap, "non-goals", earlier declined requests. Quote the line that speaks for it or against it. With nothing written, say so; do not invent a scope.
5. **What it would touch.** Name the files and the surfaces: commands, settings, stored data, the public API. Say whether it changes behaviour for people who do not use it, and whether it needs a migration.
6. **What it would cost.** A rough size (hours, a day, days), what must be tested, what must be documented, and what it adds to maintain for good.
7. **A smaller version**, when there is one that meets the need.

## Verdicts

| Verdict | When |
|---|---|
| Exists already | Show how |
| Fits, small | Inside the stated scope, a contained change. Recommend doing it |
| Fits, large | Inside the scope, but it needs a design. Recommend a design note first |
| Owner's call | The scope says nothing either way. Give the case for and against |
| Does not fit | A written non-goal, or it breaks something the project promises. Quote it |
| Duplicate | Link the earlier one |

## After the verdict

Stop. When the owner says yes, write the task in your own words, with a "done when" list, and show it to them. From then on it is the owner's own task: it leaves `triage`, and `deliver` takes it with the ending the owner gives for it, which is stop unless they say otherwise.
