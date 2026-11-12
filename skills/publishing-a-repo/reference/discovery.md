# Discovery

How a public repo gets found and correctly summarised by search engines, AI search and coding agents.

## The honest model

Two things are often treated as one.

- **Discovery**: can a searcher or an agent find the repo when it looks for something like it? Signals outside the README decide most of this.
- **Matching and summary**: once found, does the text match the query and get summarised correctly? The README and the docs decide this.

A good README improves matching. It does not make a repo discoverable. Say this to the owner in plain terms, and do not promise traffic.

## Levers, strongest first

1. **Public.** Nothing indexes a private repo. Until the owner publishes, everything below is preparation.
2. **Repo metadata.** The description and topics on the host, and the description and keywords in the package manifest. For an agent that searches a code host or a package registry, this is usually the strongest lever.
3. **README first sentence and structure.** Covered by `writing-readmes`.
4. **`llms.txt`**, only for a hosted docs site.
5. **Presence elsewhere.** The package registry listing, links from other sites, curated lists. Outside the repo and slow, with the highest ceiling.

## Check and propose

**First sentence.** It states what the project is in the words people search for: the category ("CLI", "MCP server", "ORM", "feature-flag service"), the stack or protocol, and the deployment model if it matters ("self-hosted"). "An alternative to X" is allowed only when it is true. The description, the manifest and the README say the same thing.

**Description and topics.** These are remote settings. Propose the values and the commands; the owner runs them.

```sh
gh repo edit <owner>/<repo> --description "<the first sentence>"
gh repo edit <owner>/<repo> --add-topic <topic> --add-topic <topic>
```

Topics are lowercase with hyphens, at most 20. Pick them from the category, the stack, the protocols and integrations, and the domain. Use terms that appear in the code or the docs, not aspirations.

**Package manifest.** `description`, `keywords`, `repository`, `homepage` and `license` in `package.json`, `pyproject.toml`, `Cargo.toml` or the equivalent. This is an in-repo edit: propose it with the other fixes. The `license` field waits for the owner's licence choice.

**Specific and true.** Concrete commands, real defaults and real numbers get quoted correctly. A wrong fact gets repeated by every agent that reads it.

## llms.txt

Only when the project has a hosted docs site. It is a Markdown file at the site root that maps the docs for language models, in the way a sitemap does for crawlers.

```markdown
# <Project>

> <The first sentence.>

## Docs
- [Quickstart](https://<site>/quickstart/): the shortest path to a result
- [Install](https://<site>/install/): every install path and the configuration
- [Guides](https://<site>/guides/): one page per feature

## Optional
- [Changelog](https://<site>/changelog/)
```

Use full URLs. Every link must resolve on the built site. Regenerate it when the navigation changes; some site frameworks have a plugin that does.

## For the owner

- Making the repo public.
- The final wording of the description and the set of topics.
- A social preview image, if wanted. Never reference an image that does not exist.
