# c-energie/.github

The **personal** dev-hub catalogue. `catalogue.toml` here lists c-energie repos and how a
dev machine sets them up; [dev-hub](https://github.com/c-energie/dev-hub) pulls it on every
start.

A machine follows more than one catalogue and dev-hub merges them into a single view:

```
devhub follow                       # what this machine follows
devhub follow c-energie/.github     # start following this one
devhub follow c-energie/.github --remove
```

On this setup the pair is:

| catalogue | what it declares |
|---|---|
| `net-flex/.github` | the FlexSense team repos, and the shared `[org]` facts — secret-sync, roster, context repo, plugin marketplace, GUI theme |
| `c-energie/.github` | personal repos only — no `[org]` table, because the shared facts are already stated once and inherited |

## The two rules

**A repo is declared by exactly one catalogue.** Two declarations would give its setup
rules two owners and no way to choose, so the merge refuses. `c-energie/bots` moved here
out of the team catalogue for this reason — and because a repo described as "personal
Telegram bots" had no business appearing on every FlexSense teammate's Repos page.

**Shared `[org]` keys are declared once and inherited.** `secret_sync`, `roster`,
`context` and `context_kit` describe how the *machine* is wired rather than what one org
is, so whichever catalogue states one, the others get it. Stating the same value twice is
fine; stating two different values is refused.

## Adding a repo

Add a `[repos."c-energie/<name>"]` table with at least a `description`. Omit `toolchain`
to have it auto-detected on clone. Unknown keys are a parse error, not a silent ignore —
CI runs `devhub validate catalogue.toml` on every push and PR.

Run it locally before pushing:

```
devhub validate catalogue.toml
```
