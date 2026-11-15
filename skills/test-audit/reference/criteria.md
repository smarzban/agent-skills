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
| Catches nothing | It runs no code and reads no file of this repo: it asserts on data it built, on what a mock was told to return, or on the language or a library |
| | It sits behind a second guard and passes whatever is broken on its path |
| Proved by a test of the same kind | Several cases with different values that take one path through the code |
| | An old test left beside its replacement |
| | A check that fails only on a crash ("is defined", "does not throw", "it renders", `expect(total(cart)).toBe(total(cart))`) beside a test that asserts the result |

When a convicted test was the only one that drove a flow, the flow goes on the list of flows with no test found.

## Remove, on the owner's yes

| Class | Example |
|---|---|
| Proved at another layer | A unit test whose every assertion a flow test also proves, or the reverse. The run shows it; whether one contract needs two tests is the owner's choice |
| On the list in `SKILL.md`, and proved by another test | Six rows that refuse an unknown config field on one line of code. The list protects the contract; which tests it needs is the owner's choice |

## Task

| Class | Example | Done when |
|---|---|---|
| Tied to internals, and nothing that stays proves what it guards | It asserts the order of internal calls; it asserts on a mock of this repo's own code; it pins the exact wording of an error where callers branch on the type; a large snapshot nobody reads | A new test fails for <the break> and passes when only the internals change. The old test is removed in the same change |
| Checks less than the code promises, and is the only test of that path | Only "is defined" on code that returns a total | It asserts what the code promises |
| The name promises what the test never exercises | Named for a refund; asserts nothing about one | It tests that, or is renamed |
| May be hiding a bug | Its expected value contradicts the docs, a spec or another test | The owner knows which is right; the code is fixed if it is the bug, the test if not |
| Does not run | Skipped or disabled; left out by a config or an `only`; it returns before its assertion | It runs and passes, or the owner decided it goes |
| A test on the list in `SKILL.md` that catches nothing | A regression test that passes with the fix reverted; a default pinned by a tautology | It fails when the fix or the default is gone |
| Passes or fails by chance | A fixed sleep before an assert; the clock; test order, or state another test leaves; a shared port, temp path or env var; the network | It gives one result on every run |
| Production code only tests use | An export or a flag with no caller in the product. A hook that stands in for something outside the repo is not one | It has a caller in the product, or it goes with its test |

## Hard cases

- **The same kind** means the same claim at the same layer: both tests call the same function or drive the same interface. A test that reaches the code through its caller is another layer.
- **A literal expected value** is not a "computed" one. A test that pins `"60.15 USD"` fails when the total changes, so it catches something. It is a task only when the docs or a spec say the value is wrong.
- **A mock** of something outside the repo (the network, the clock, a payment service) is fine. A mock of this repo's own code is a task when the test asserts on it, or when it replaces the thing the test's name says it tests or everything around it. When the run shows such a test catches nothing, it is a Remove.
- **A snapshot** that is small and read by people, such as a command's output, is a contract. A large one that is regenerated without reading is tied to internals.
- **A table of cases** is judged case by case when the framework reports each one. Removing a row is a removal. Cases in a loop inside one test are one test.
- **A test of a private helper** that holds logic the interface cannot reach cheaply is a Keep.
- **A test that pins text** (a prompt, a doc, a manifest) is a Keep when that text is the product or the repo states it as a requirement. Otherwise it stays unless a run convicts it.
- **A test nobody seems to rely on** (a getter, a constant restated, an input a guard upstream refuses) stays unless a run convicts it. Say so at the checkpoint, under what you are unsure of.

## Flows with no test found

Take the list from the repo's `verify-*` feature map, or from the commands, routes and rules its docs name. For each, look for a test that drives it through the interface its user uses; a rule that only other code calls is covered by a test of that function. Call the rest "no test found": you read for them, you did not prove it. Done for such a task: "A test drives <the flow> through <its interface> and fails when the flow is broken."
