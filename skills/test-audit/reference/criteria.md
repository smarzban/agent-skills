# Criteria

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
| Catches nothing | It runs no code of this repo: it asserts on data it built, on what a mock was told to return, or on the language or a library |
| | The expected value is computed by the code under test: `expect(total(cart)).toBe(total(cart))` |
| Proved by a test of the same kind | Several cases with different values that take one path through the code |
| | An old test left beside its replacement |
| | A check that fails only on a crash ("is defined", "does not throw", "it renders") beside a test that asserts the result |

When a convicted test was the only one that drove a flow, the flow goes on the list of flows with no test found.

## Remove, on the owner's yes for the class

| Class | Example |
|---|---|
| Proved at another layer | A unit test whose every assertion a flow test also proves, or the reverse. The run shows it; whether one contract needs two tests is the owner's choice |
| Nobody relies on it | A getter or a setter; arguments passed straight through; formatting that no caller reads |
| | What the compiler or the type checker already guarantees |
| | A constant restated, a list copied from the source, or a search of the source text: it fails only when someone edits on purpose |
| | An input no real caller or boundary can produce. Name the guard that makes it unreachable |

The second class does fail when its code is broken, so no run removes it. The verdict is a judgement about worth, and that is the owner's. A test on the list that is never removed is in neither class.

## Task

| Class | Example | The task |
|---|---|---|
| Tied to internals, and nothing that stays proves what it guards | It asserts the order of internal calls; it asserts on a mock of this repo's own code; it pins the exact wording of an error where callers branch on the type; a large snapshot nobody reads | Rewrite it through the interface its user uses |
| Checks less than the code promises, and is the only test of it | Only "is defined" on code that returns a total | Assert what the code promises |
| The name promises what the test never exercises | Named for a refund; asserts nothing about one | Make it test that, or rename it |
| May be hiding a bug | Its expected value contradicts the docs, a spec or another test | Find which is right. Fix the code if it is the bug, the test if not |
| Does not run | Skipped or disabled; left out by a config or an `only`; it returns before its assertion | Run it and fix it, or decide it goes |
| A bug's guard that catches nothing | A regression test that passes with the fix reverted | Restore the guard |
| Passes or fails by chance | A fixed sleep before an assert; the clock; test order, or state another test leaves; a shared port, temp path or env var; the network | Make it deterministic |
| Production code only tests use | An export, a flag or an injection hook with no caller in the product | Delete it with its test |

## Hard cases

- **A literal expected value** is not a "computed" one. A test that pins `"60.15 USD"` fails when the total changes, so it catches something. It is a task only when the docs or a spec say the value is wrong.
- **A mock** of something outside the repo (the network, the clock, a payment service) is fine. A mock of this repo's own code is a task when the test asserts on it, or when it replaces the thing the test's name says it tests or everything around it.
- **A snapshot** that is small and read by people, such as a command's output, is a contract. A large one that is regenerated without reading is tied to internals.
- **A table of cases** is judged case by case when the framework reports each one. Removing a row is a removal.
- **A test of a private helper** that holds logic the interface cannot reach cheaply is a Keep.
- **A test that pins text** (a prompt, a doc, a manifest) is a Keep when that text is the product or the repo states it as a requirement. Otherwise it is a constant restated.
