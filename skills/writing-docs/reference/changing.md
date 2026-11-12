# Scope: changing

Docs for the people and agents who modify the code. They can read the code for what it does. These pages hold what the code cannot say: why it is built this way, and what must not break.

## Pages

| Page | Kind | Holds |
|---|---|---|
| Architecture | explanation | The components, the main data and control flows, and the constraints that shaped the design, each with its evidence. |
| Data model | reference | Every persisted or wire-format entity. |
| One page per subsystem | explanation | Responsibility, entry points, mechanism, invariants, error paths. |
| Security model | explanation | Only when a trust boundary exists: what is trusted, where each check lives, what fails open and what fails closed. |
| Dev setup | how-to | Clone to a passing test run. Every command taken from CI and run. |
| Contributing | how-to | The commit convention as seen in `git log`, the branch and review flow, what a change must include. |

## Ledger

One row per item, enumerated from the code:

| Row kind | Enumerate from |
|---|---|
| Module or subsystem | the filesystem and the build config |
| Data model | schema files, migrations, model definitions |
| Entry point | command registrations, route table, jobs, hooks |
| Invariant | types, assertions, constraints, tests that name a rule, comments that state one |

```
| Item              | Kind        | Page                 | Status            |
|-------------------|-------------|----------------------|-------------------|
| src/store/        | subsystem   | internals/store.md   | done              |
| Task, Step        | data model  | data-model.md        | done              |
| mytool add        | entry point | internals/cli.md     | done              |
| ids never reused  | invariant   | internals/store.md   | done, unenforced  |
| src/vendor/       | excluded    |                      | vendored          |
```

Functions and private helpers are not rows. A private detail earns a mention only when it carries an invariant, such as a lock order or a sentinel value.

## Depth: what "explained" means

- **Architecture**: a new engineer can predict where a given change belongs, and knows the two or three constraints behind the design.
- **Subsystem**: a maintainer can change it without breaking an invariant they were never told about.
- **Data model**: someone writing a migration knows every consumer of the field they touch.

A page that does not get its reader there is a summary. Deepen it or fold it into another page. A page that restates the code line by line is waste; describe the mechanism and stop.

## Subsystem page

```markdown
# <Subsystem>

**Responsibility.** What it owns, and what it deliberately does not.

**Entry points.** How other code reaches it, with paths.

**How it works.** The moving parts and the main flows. Enough to know where to look.

**Invariants.**
- <rule>. Enforced by: <type, test, lint or constraint, with its path>. If violated: <what breaks>.
- <rule>. Unenforced. If violated: <what breaks>.

**Error paths.** What fails, how it surfaces, what is retried and what is fatal.

**Why it is this way.** <constraint or decision> (<ADR, commit, pull request or comment>). Or: rationale unknown.
```

## Data-model entry

```markdown
## <Entity>

What it represents, who creates, changes and deletes it, and its lifecycle.

| Field | Type | Meaning, units, valid states |

Relations and the integrity rules between them.
Consumers: <the code that reads each field>.
Verified against: <schema file or migration>.
```

## Rationale

Search for evidence before you write a why: ADRs and design notes, `git log -S<symbol>` and `git log --follow <path>` for the commit that introduced a decision, the pull request it links, comments beside the code. Quote or link what you find. Asking the owner is evidence too; say that the owner stated it. Never infer a motive from the shape of the code.

## Public reference for a library

Only when code outside the repo calls the API. Use the ecosystem's generator (rustdoc, typedoc, Sphinx autodoc, godoc) where one exists, document how to run it, and keep hand-written pages for concepts and guides. Adding a generator to the build is a change for the owner to approve.
