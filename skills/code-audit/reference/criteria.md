# Criteria

## Where a search stops

A name can be used with no import of it. Before you call a thing unused, look for each of these:

- **A string or a built name.** A dynamic import, a registry keyed by name, reflection, a template, a route table, a command name looked up at run time.
- **A convention.** A framework that loads files by their folder or their name (routes, pages, migrations, tests, plugins), a decorator, dependency injection, a hook the runtime calls.
- **The manifest and the tooling.** `bin`, `exports`, `main`, scripts, the CI files, the container files, the build and lint config.
- **The docs.** A command, an import or an option the docs tell a user to use.
- **Another reader of the same thing.** Another language or another repo that reads the same file, table, message or environment variable; a query that names a column; a saved file that holds the old name.
- **The package's own users.** In a library, every export of an entry point has callers you cannot see.

## Remove, when the search and the run agree

| Class | Example |
|---|---|
| A private thing nothing refers to | A function, a constant or a type that is not exported and not named anywhere |
| A file no entry point reaches | A module left behind by a move, imported by nothing and named by no config |
| A dependency nothing uses | In the manifest, imported by no file, named by no script or config |

## Remove, on the owner's yes

| Class | Example |
|---|---|
| On the public surface, with no use in the repo | An export of an entry point; a command or a flag no doc names |
| Only tests use it | A class with a test file and no caller in the product. A hook that stands in for something outside the repo is not one |
| Named in the manifest, used by nothing | A script in the manifest that no doc and no CI file runs |
| Left for compatibility | A shim or an old name whose callers are gone |
| A setting nothing reads | A key in a config file of the repo that no code looks up |

## Task

| Class | The failure | Done when |
|---|---|---|
| Two copies of one rule | A fix lands in one and the other stays wrong; or they already differ | One place holds the rule and the other uses it |
| Two lists kept in step by hand | An item is added to one list only | One list is derived from the other, or a check fails when they differ. A case for `correct` |
| Two ways to do one thing | New code copies whichever it finds first, so both keep callers | Callers use one way and the other is deleted in the same change |
| A layer that hides nothing | A wrapper that passes its arguments on, an option nobody sets: a reader must learn both sides, and a change must be made twice | The layer is gone and its callers call what it wrapped |
| One piece of state with two writers | A change to one writer breaks a rule only the other keeps | One owner writes it; the rest ask the owner |
| Flags that must agree | Two booleans whose combinations are not all legal drift into one that is not | One value holds the state |
| A check repeated inside, or missing at the edge | Input is checked in three inner functions and not where it enters, so a new path skips it. When the gap is a security weakness, the Rules say where it goes | It is checked once, where it enters |
| An inside detail that leaks out | Callers depend on a storage or wire type, so changing it breaks them | Callers get the project's own type |
| Internals that outside code can import | Outside code imports them and they become the interface | The import fails the build. A case for `correct` |
| Dead weight inside used code | A branch that cannot run or changes no result: a maintainer plans a change around a path that never happens | It is gone, and behaviour is shown unchanged |
| A fault hidden, not fixed | A catch that drops the error; a retry around this repo's own fault: the failure reaches the user later, with no trace | The fault is fixed, or reported where it happens |
| Code and docs disagree | The docs promise one result and the code gives another; the docs name a setting no code reads | The owner knows which is right; the wrong one is fixed |
| Drift in config or dependencies | Two versions of one library in the project's own manifests; one value set in two places that differ | One version, one place |

For every task that moves structure, done also means: a test pins the behaviour before anything moves, and it still passes after.

## Drop

- A preference for smaller, newer or differently named code.
- A layer with one caller that names a real idea of the domain, or that the docs present as the place to extend.
- Duplication of a few lines that hold no rule.
- Anything you cannot give a failure and a person for.

## Hard cases

- **An export nothing in the repo imports, in a file that is not an entry point and that users of the package cannot import.** It is private in effect: a Remove, once the search shows no built name reaches the file.
- **A type or an interface.** The type check is its run.
- **A script or a tool for people.** A file under `scripts/` that a person runs by hand has no caller. It is on the list: ask.
- **A test helper.** It is test code. Leave it to `test-audit`.
- **A feature that works but that nobody may want.** Not unused. Whether it stays is the owner's product choice, and not a finding here.
