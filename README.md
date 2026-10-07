# c-energie/.github

Org-wide defaults for c-energie repos: community health files and reusable workflows.

The personal dev-hub catalogue that used to live here is now in
[`c-energie/dev-hub-catalogue`](https://github.com/c-energie/dev-hub-catalogue).

## Reusable workflows

### `unittest.yml`

Installs a member, with its git upstreams, and runs its `unittest` suite across a
matrix of OS and Python. Each member's `tests.yml` calls it, pinned `@main`. The path
`c-energie/.github/.github/workflows/unittest.yml` and the inputs below are the
contract every caller depends on: renaming the file or an input, or making an input
required, breaks every caller, and nothing here lists them.

| input | default | meaning |
|---|---|---|
| `upstreams` | `""` | Git requirements installed first, one per line, e.g. `building-energy-analysis[smeter-models] @ git+https://github.com/c-energie/building-energy-analysis`. Give **no `@ref`**: each resolves to its repo's default branch. |
| `extras` | `""` | Extras for `uv pip install -e ".[<extras>]"`; empty installs plain `-e .`. |
| `tier` | `fast` | Exported as `TEST_TIER`: `fast`, `slow` or `benchmark`. Anything else fails the run. |
| `matrix` | `pr` | `pr` = ubuntu {3.11, 3.13} + windows 3.13; `full` = {ubuntu, windows} × {3.11, 3.12, 3.13}. Anything else fails the run. |
| `import_only` | `""` | A module name. When set, `python -c "import <it>"` replaces the suite; the installs still run. |
| `working-directory` | `.` | Member root to install, test and collect logs from, relative to the checkout. |

Every leg runs with `CI=true`. A failing leg uploads `<working-directory>/tests/Logs/`
as an artifact; a passing leg uploads nothing.

**Secret: `C_ENERGIE_READ_TOKEN`** (optional). A fine-grained PAT with contents:read on
the private c-energie repos, used only to fetch private upstreams. It is a
**repository** secret in each member, not an org secret: the org is on GitHub Free,
which cannot share org secrets with private repos. So a caller must pass it
explicitly, as below; a called workflow never sees a secret it was not given. When it
is empty or absent, the workflow skips authentication and carries on, which is what
should happen once the upstreams are public and the PAT is revoked.

Never put git URLs or `[tool.uv.sources]` in a member's `pyproject.toml` to make CI
find an upstream; that is what `upstreams` is for.

Minimal caller, `.github/workflows/tests.yml` in a member:

```yaml
name: tests

on:
  push:
    branches: [master]  # the member's default branch; main for serl-dataset and ghg-dataset
  pull_request:

permissions:
  contents: read

jobs:
  unittest:
    uses: c-energie/.github/.github/workflows/unittest.yml@main
    with:
      upstreams: |
        building-energy-analysis[smeter-models] @ git+https://github.com/c-energie/building-energy-analysis
    secrets:
      C_ENERGIE_READ_TOKEN: ${{ secrets.C_ENERGIE_READ_TOKEN }}
```
