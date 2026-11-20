# Databricks bundle

Deploys the benchmark jobs, one SQL warehouse and one all-purpose cluster to a workspace.

## Deploy

1. Import the [databricks-tpc-di](https://github.com/shannon-barrow/databricks-tpc-di) repo into your workspace.
2. Make sure the scale factor 10 data exists in the volume the jobs read from.
3. Deploy, passing the workspace path of that repo:

```bash
databricks bundle deploy -p <profile> --var tpcdi_path=/Workspace/Users/<you>/databricks-tpc-di
databricks bundle run jc_2xlarge_sf10 -p <profile> --var tpcdi_path=/Workspace/Users/<you>/databricks-tpc-di
```

Run any job by its key, for example `srv_standard_sf10` or `wh_large_sf10`. The deploy starts the all-purpose cluster and the warehouse, so stop them if you don't run the jobs right away.

## How the jobs are built

[`resources/benchmark_sf10.yml`](resources/benchmark_sf10.yml) is generated. Don't edit it. Change [`scripts/gen_resources.py`](scripts/gen_resources.py) and run it:

```bash
python3 scripts/gen_resources.py
```

The script copies one 25-task workflow from [`serverless_job_template.json`](scripts/serverless_job_template.json) and swaps the compute for each config. It needs `pyyaml`.

## Serverless jobs with a size limit

Small, Medium and Large limits are set on a serverless compute object. Create the objects in the workspace (Compute, then Serverless), then put their IDs in [`scripts/serverless_ids.json`](scripts/serverless_ids.json) under `small`, `medium` and `large`. Run the script again and the three jobs appear. The limits are in beta and a workspace admin has to turn them on.

## Settings worth knowing

- Photon is set on every classic cluster except `jc_2xlarge_nophoton`.
- The all-purpose cluster stops after 10 idle minutes. The warehouse stops after 10.
- Spot only applies to workers. The driver stays on-demand.
