# Prose style for docs

Shared by the writing skills. Apply it to every page you write or edit.

## One kind per page

Pick the kind before you write, and keep the others out. Split and link when a page needs two.

| Kind | Reader wants | Shape |
|---|---|---|
| Tutorial | to learn by doing | Numbered steps, each with a visible result. One clause of explanation at most. |
| How-to | to finish a task | Steps to one goal, named by the task ("Rotate the API key"). No background. |
| Reference | to look something up | Facts only, laid out like the thing described. Generated from code where a generator exists. |
| Explanation | to understand why | One bounded topic: constraints, trade-offs, alternatives. The only kind that holds opinion. |

## Words

- Write the real symbol, path, flag and command name in code font: `parse_config()`, `src/store.rs`, `--dry-run`. Do not describe it ("the config parser").
- Give each thing one name and use it everywhere. Take the name from the code.
- Use the short everyday word. Cut any word the sentence survives without.
- Leave out marketing adjectives ("powerful", "seamless", "blazing fast") and words that judge difficulty ("simply", "just", "easy").
- Headings are plain text in sentence case. No emoji.

## Sentences

- Put the condition before the instruction: "To reset the store, run `mytool clean`."
- Write instructions as commands, one per sentence. Address the reader as "you".
- State the common case first and the exception after it.
- Make every "it" and "this" point at one obvious noun. Repeat the noun when in doubt.

## Commands and examples

- Put every command in its own fenced block, exactly as typed, with no prompt character.
- Show the expected output, or say what the reader should see, after any command whose result matters.
- Run each example before you publish it.

## Numbers

Every count, size and version on the page is true at the commit that lands it. Put the command that regenerates the number beside it, or leave the number out.

```sh
git ls-files 'src/*.rs' | wc -l
```

## Links

Link text says where the link goes: the page title or the file path. Each page can be read on its own: state the fact, then link for depth.
