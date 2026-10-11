# c-energie

Open tooling for estimating the energy performance of real homes from their metered data.

At its centre is **building-energy-analysis (BEA)**, a Python framework that turns metered
household energy data into fitted estimates of a dwelling's thermal performance, above all its
heat transfer coefficient (HTC). It loads, cleans and filters a cohort of households, fits models
to each one and compares the estimates. Data sources come in through **cohort adapters**, and
models through **model packages**, so the same analysis runs over any cohort with any model.

**Start here: the [BEA documentation](https://c-energie.github.io/bea-docs/).**

## Packages

| Package | What it is |
|---|---|
| [`building-energy-analysis`](https://github.com/c-energie/building-energy-analysis) | The framework: cohorts, cleaning, filtering, models, fitting and comparison. |
| [`project-configuration`](https://github.com/c-energie/project-configuration) | The base layer BEA builds on: layered TOML configuration, results storage, filtering, logging and the shared test harness. |
| [`smeter-test-cohort`](https://github.com/c-energie/smeter-test-cohort) | Cohort adapter for the SMETER TEST data: a synthetic EnergyPlus phase and a monitored phase. |
| [`serl-cohort`](https://github.com/c-energie/serl-cohort) | Cohort adapter for the Smart Energy Research Lab (SERL) Observatory data. |
| [`ghg-cohort`](https://github.com/c-energie/ghg-cohort) | Cohort adapter for the Green Homes Grant (GHG) monitoring data, built on `serl-cohort`. |

The cohort adapters hold code only. The data they read is never in these repositories, and is
obtained from its holders under their own terms.

## Bring your own data or models

- [`bea-cohort-template`](https://github.com/c-energie/bea-cohort-template): a copier template
  for a cohort adapter, turning your data source into BEA cohorts.
- [`bea-model-template`](https://github.com/c-energie/bea-model-template): a copier template for
  a model package, fitting your models to any cohort.

To contribute, read the [contributing guide](https://github.com/c-energie/.github/blob/main/CONTRIBUTING.md).
