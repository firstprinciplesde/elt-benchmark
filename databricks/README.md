# Databricks bundle

Deploys one job per compute config. Each job does a full load, then 29 simulated incremental days.

## Deploy

1. Import the [databricks-tpc-di](https://github.com/shannon-barrow/databricks-tpc-di) repo into your workspace.
2. Run its `AugmentedGen` job for your scale factor. This stages the daily files and tables in a Unity Catalog volume. Do it once per scale factor.
3. Deploy, passing the workspace path of that repo:

```bash
databricks bundle deploy -p <profile> --var tpcdi_path=/Workspace/Users/<you>/databricks-tpc-di
databricks bundle run jc_2xlarge_sf10 -p <profile> --var tpcdi_path=/Workspace/Users/<you>/databricks-tpc-di
```

Run any job by its key, for example `serverless_standard_sf10`. Job names look like `etl-benchmark-aug-<config>-sf10`.

## How the jobs are built

[`resources/augmented_sf10.yml`](resources/augmented_sf10.yml) is generated. Don't edit it. Change [`scripts/gen_augmented.py`](scripts/gen_augmented.py) and run it:

```bash
python3 scripts/gen_augmented.py
```

The script takes the setup task and the 17-task day from [`augmented_template.json`](scripts/augmented_template.json), then chains 29 copies of the day after the full load. Each day starts when the previous day's last tasks finish. Task keys get a `_dNN` suffix, so every job has 496 tasks. It needs `pyyaml`.

The repo's own design is a parent job that loops over a child job. A loop can wrap only one task, so this bundle unrolls the days instead.

[`single_batch/`](single_batch/) holds the older layout, one batch per job. It isn't deployed.

## Serverless jobs with a size limit

Small, Medium and Large limits are set on a serverless compute object. Create the objects in the workspace (Compute, then Serverless), then put their IDs in [`scripts/serverless_ids.json`](scripts/serverless_ids.json) under `small`, `medium` and `large`. The generator doesn't read them yet. The limits are in beta and a workspace admin has to turn them on.

## Settings worth knowing

- Photon is on for every classic cluster except `jc_2xlarge_nophoton`.
- Spot only applies to workers. The driver stays on-demand.
- Each job drops its schema in a final cleanup task unless you set `delete_when_finished` to false.
