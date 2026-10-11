# Contributing to c-energie

This guide is the default for every c-energie repository that has no `CONTRIBUTING.md` of its
own. It covers what all the packages share; each repository's `README.md` has a **Commands**
section for anything specific to it. It applies equally to work written by hand and work written
with a coding agent: an agent's instructions file should point here rather than repeat it.

## 1. Where to ask

- **Questions, bugs and proposals:** open an issue in the repository concerned and pick one of
  the issue forms. Blank issues are switched off, because the bug form's fields (package and
  version, OS, Python, install route) are what makes a report reproducible.
- **How the packages fit together:** the umbrella docs at
  <https://c-energie.github.io/bea-docs/> hold the tutorials, the how-to guides, the
  explanation of the three contracts and the package map. Read them before asking how to start a
  cohort adapter or a model package.

## 2. Development setup

Install the repository editable, with the extras you need:

```bash
uv pip install -e ".[<extras>]"
```

Until the packages are on PyPI, install the first-party packages it depends on from their git
upstreams first, always from `@master`:

```bash
uv pip install "project-configuration @ git+https://github.com/c-energie/project-configuration@master" \
               "building-energy-analysis[<extras>] @ git+https://github.com/c-energie/building-energy-analysis@master"
uv pip install -e ".[<extras>]"
```

The git URLs belong on the command line, never in a `pyproject.toml`: once the upstreams are
installed, pip sees their names already satisfied and does not go to an index. A git URL committed
to `pyproject.toml` would stop working at the PyPI release and cannot be uploaded there. CI does the
same thing through its `upstreams` input.

## 3. Running tests

The packages use `unittest`, through the shared harness in `project-configuration`; pytest is
not needed.

- **Run from the member root**, the directory holding the package's `pyproject.toml`:

  ```bash
  python -m unittest discover
  ```

  That is the only place a fresh checkout runs with no other setup.

- **Tiers.** Tests are grouped into `fast` (the default), `slow` and `benchmark`. Choose one with
  the `TEST_TIER` environment variable, for example `TEST_TIER=slow`. Before opening a pull
  request, the `fast` tier should pass locally.

- **A class that passes is switched off for the next run in the same tree.** The first run writes
  `tests/Configuration_files/TestSettings_config.toml`, and each passing class is turned off in
  it so that a re-run spends its time on what failed. A fresh checkout runs everything. To run
  everything again in an existing tree, delete
  `tests/Configuration_files/TestSettings_config.toml`.

- **Tests marked `requires_data` skip when their input data is not configured.** Some tests need
  real data that cannot be published. They skip, and the skip reason names the missing setting.
  A skip is not a pass: if you meant to run those tests, configure the input path the reason
  names.

## 4. Cross-repository changes

When a change spans packages, **merge the upstream change first**, then open the downstream pull
request. CI installs every upstream from its default branch, with no matching of same-named
branches, so a downstream pull request that relies on an unmerged upstream change fails CI.

## 5. Contract changes

The packages meet at three public contracts, each with a version constant:
`MODEL_CONTRACT_VERSION` and `COHORT_CONTRACT_VERSION` in `building-energy-analysis`, and
`RESULTS_CONTRACT_VERSION` in `project-configuration`. Cohort adapters, model packages and stored
results written by other people depend on them: a different contract major refuses to load, and
a different minor warns.

A change to a contract therefore needs:

- an ADR in that repository's `docs/adr/` recording the change and why; and
- a decision on the matching `*_CONTRACT_VERSION`: no bump, a minor bump or a major bump, stated
  in the pull request. A major bump also needs a new major release of the package.

## 6. Vocabulary

The packages share the glossary in
[`building-energy-analysis`'s `CONTEXT.md`](https://github.com/c-energie/building-energy-analysis/blob/master/CONTEXT.md).
Use its terms in code, docs and issues. The one most often got wrong: a data source (SERL,
SMETER TEST) is never a "dataset". In this vocabulary a dataset is one household's data. The
collection of households is a cohort, and the package that turns a data source into cohorts is a
cohort adapter.

## 7. Licence of contributions

Contributions are licensed under the licence of the repository they are made to, which is MIT
for the published c-energie packages (inbound = outbound). There is no contributor licence
agreement to sign.
