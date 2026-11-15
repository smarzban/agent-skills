# Criteria

Ask of each test: if this behaviour broke tomorrow, would this test be the one that tells us? And if someone tidied the code without changing what it does, would this test stay quiet?

## Keep

| Class | Example |
|---|---|
| Drives a flow the way its user does | Runs the command and reads its output; sends the request and reads the response |
| Pins a contract a caller relies on | A function, a concrete input, and an expected value taken from the requirement |
| Pins an invariant | A key that must exist in two tables; a compile-time type check |
| Guards a bug that happened | It names the issue and fails without the fix |
| Checks what an outside service receives | The payload sent to a mocked payment API |
| A promise that something does not happen | "An empty cart never charges": the payment client got no call, beside a case where it does |

## Remove, when the run convicts it

| Class | Example |
|---|---|
| Catches nothing | The only check is "is defined", "is truthy" or "does not throw" where the code promises more |
| | The expected value is computed by the code under test: `expect(total(cart)).toBe(total(cart))` |
| | It asserts on data the test built itself, or that a mock returned what the test told it to return |
| | Its assertion sits in a branch or a callback that never runs |
| Redundant | Another test fails for every break it catches |
| | Several cases with different values that take one path through the code |

## Remove, on the owner's yes for the class

| Class | Example |
|---|---|
| Nobody relies on it | A getter or a setter; arguments passed straight through; formatting that no caller reads |
| | What the compiler or the type checker already guarantees |
| | The behaviour of a library or the framework, not of this repo |
| | A constant restated: it fails only when someone edits the constant on purpose |
| | Written to touch lines: it calls the code and claims nothing about it |

These tests do fail when their code is broken, so no run convicts them. The verdict is a judgement about worth, and that is the owner's.

## Task

| Class | Example | The task |
|---|---|---|
| Tied to internals | It asserts the order of internal calls or calls a private helper; it mocks this repo's own modules; it pins the exact wording of an error where callers branch on the type; a large snapshot nobody reads | Rewrite it through the interface its user uses |
| May be hiding a bug | Its expected value contradicts the docs, a spec or another test; it was changed in the same commit as the code with no reason given | Find which is right. Fix the code if it is the bug, the test if not |
| Does not run | Skipped or disabled; left out by a config or an `only`; it returns before its assertion | Run it and fix it, or decide it goes |
| A bug's guard that catches nothing | A regression test that passes with the fix reverted | Restore the guard |
| Passes or fails by chance | It depends on timing, order, the clock or the network | Make it deterministic |
| Claims a flow and catches nothing | The run convicted it, and no other test drives that flow | Removed in this run; the flow goes on the list of flows with no test found |

## Hard cases

- **A literal expected value** is not a "computed" one. A test that pins `"60.15 USD"` fails when the total changes, so it catches something. It is a task only when the docs or a spec say the value is wrong.
- **A mock** of something outside the repo (the network, the clock, a payment service) is fine. A mock of this repo's own code is a task when it replaces the thing the test's name says it tests, or everything around it.
- **A snapshot** that is small and read by people, such as a command's output, is a contract. A large one that is regenerated without reading is tied to internals.
- **"It renders" or "it starts"** fails when the code crashes. It is a Keep unless another test catches the same breaks.
- **A table of cases** is judged case by case when the framework reports each one. Removing a row is a removal.
- **A test of a private helper** that holds logic the interface cannot reach cheaply is a Keep. Say so in the report; it may mean the helper should be its own module.
