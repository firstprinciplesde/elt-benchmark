# elt-benchmark

**An open benchmark of what the same ETL pipeline costs, and how long it takes, on every compute option and setting, so you can pick compute from numbers instead of guesses.**

Part of [First Principles Data Engineering](https://www.youtube.com/@FirstPrinciplesDE). The first benchmark is Databricks compute, run on a TPC-DI derived ETL workload.

> [!NOTE]
> Work in progress. There are no results here yet, and the benchmark code is added after review. This page describes the plan, so nothing below is a claim about results.

## What it will do

- Run one ETL pipeline, derived from the TPC-DI benchmark, at several data sizes (scale factors) on each compute option and setting.
- Repeat every configuration several times and report the median and the spread, not a single run.
- Measure runtime, start-up wait, DBUs and total cost per run, including the cloud bill for classic compute.
- Publish the setup code and the queries that read cost, so you can rerun it on your own workspace and compare.

## First benchmark: Databricks compute

| Compute option | Settings varied |
|---|---|
| Serverless jobs | performance optimized, standard |
| Classic jobs compute | Photon, spot, worker count and type |
| All-purpose compute | same as classic jobs |
| Pools | with and without a pool |
| SQL warehouses | serverless, pro, classic, size |

## What is next

I will be benchmarking other cloud providers and compute options in this repo too. Each gets its own folder and the same method, so results can be compared.

## Notes on TPC-DI

Results from this repo are derived from TPC-DI and are not official TPC-DI results. They are not comparable to published TPC-DI results.

## License

[MIT](LICENSE)
