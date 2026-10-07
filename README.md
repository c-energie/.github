# c-energie/.github

Org-wide defaults for c-energie repos: community health files and reusable workflows.

The personal dev-hub catalogue that used to live here is now in
[`c-energie/dev-hub-catalogue`](https://github.com/c-energie/dev-hub-catalogue).

## Reusable workflows

### `pages.yml`

Builds a repo's docs site with [Zensical](https://zensical.org) and, when asked,
publishes it to GitHub Pages. Each repo with a site calls it, pinned `@main`. The path
`c-energie/.github/.github/workflows/pages.yml` and the inputs below are the contract
every caller depends on: renaming the file or an input, or making an input required,
breaks every caller, and nothing here lists them.

| input | default | meaning |
|---|---|---|
| `working-directory` | `.` | Directory holding the site's config, relative to the checkout. The site is built into `site/` under it, so do not set `site_dir`. |
| `deploy` | `false` | Publish to Pages. Leave it false until the repo is public and Settings → Pages → Source is "GitHub Actions": on GitHub Free a private repo cannot publish. |

The build always runs on Python 3.13 with a pinned Zensical, as
`zensical build --strict`. Any warning fails it, a broken link included, so a dead link
fails the PR instead of shipping. Keep each site's config to what Material for MkDocs
9.7 also accepts: if Zensical breaks, the workflow's fallback is
`uvx --with mkdocs-material==9.7.* mkdocs==1.6.1 build --strict`, and that only builds
the same site if the config never relied on anything Zensical-only.

**The caller must grant `pages: write` and `id-token: write`, even with
`deploy: false`.** GitHub checks every job in a called workflow against the caller's
permissions before it evaluates any `if`, so a caller that grants less fails to start
instead of just skipping the deploy job. The build job itself only uses
`contents: read` and `pages: read`.

Minimal caller, `.github/workflows/docs.yml`:

```yaml
name: docs

on:
  push:
    branches: [master]  # the repo's default branch
  pull_request:

jobs:
  pages:
    uses: c-energie/.github/.github/workflows/pages.yml@main
    permissions:
      contents: read
      pages: write
      id-token: write
    with:
      # At the public flip: true on pushes to the default branch only, e.g.
      # ${{ github.event_name == 'push' }}
      deploy: false
```
