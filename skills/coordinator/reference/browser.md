# Driving a browser

For your own checks: the live test in step 9 and the page checks or UI walks after a deploy. A builder's proof and the project's `verify-<app>` skill are not covered here.

## Which tool

- **agent-browser**, when `command -v agent-browser` finds it on this machine. One shell command per step, and no script to write: the element list gives each control a ref, and the next command clicks that ref.
- **Playwright** otherwise, in a small script.

## agent-browser

- Steps: `open <url>`, `snapshot -i` (the interactive elements, each with a ref), `click @<ref>`, `eval '<js>'`, `screenshot <file>`, `close`.
- Use your own named session on every command, never the default shared one: `agent-browser --session <name> open <url>`. Flags go before the command. Close the session when you are done.
- To reuse a signed-in Chrome profile, only when the owner said you may, pass `--profile <profile name>`, which runs on a temporary copy. `--profile <dir>` is a persistent profile that the run writes to. To run the system Chrome, pass `--executable-path <path>`.
- The interactive snapshot can leave out some controls, such as the summary of a closed `<details>`. Reach those with a CSS selector instead of a ref.

## On production, or in a real person's signed-in profile

These hold whichever tool you use.

- Read a cookie by its name only. Never print `document.cookie` or the whole cookie list: it can hold a live sign-in token.
- A check writes nothing unless the owner asked for that change. Prefer GET pages, block the routes that write (with agent-browser, `network route <url> --abort`, set before you navigate; it blocks every request to that URL, reads too), and put back any preference you changed, such as a theme or a folded panel.
