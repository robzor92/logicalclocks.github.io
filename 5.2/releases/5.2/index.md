# Release notes { #hopsworks-5-2 }

The releases of Hopsworks 5.2, newest first.

## 5.2.2 { #hopsworks-5-2-2 }

Helm chart 5.2.2 · Released on 2026-10-28 · Changes since [5.2.1][hopsworks-5-2-1] · Python client [5.2.2](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.2.2)

### What's changed

- <span class="hops-release-tag">API</span> Addressed vulnerabilities in the ArrowFlight server.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Addressed vulnerabilities in the Trino and Superset services of projects.
- <span class="hops-release-tag">API</span> Fixed the OTLP sidecar of agent deployments with tracing running out of memory under ordinary traffic, which took the deployment out of service while the sidecar restarted; the sidecar now drops telemetry it can't keep up with instead of buffering it ([OTLP sidecar][docs-upgrade-otlp]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Fixed Python app pods staying in `ContainerCreating` after their execution ended; an app that was ready now ends only after `hopsworks.variables.pythonapp_unready_timeout_seconds` (600) without a ready container.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added GPU scheduling through NVIDIA Run:ai (`hopsworks.variables.run_ai_enable`, off by default), which binds every project to a Run:ai project so that Run:ai quotas, fair share and preemption apply to its pods, shows the project's GPU quota in the UI and adds `Project.get_gpu_availability()` to the Python client, which reports the cluster's GPUs when Run:ai is off; it can't be combined with Kueue ([Run:ai][docs-run-ai]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added SharePoint data sources, which copy a folder of a SharePoint Online document library into a project dataset once or on a schedule, also with `SharePointConnector.create_sync` and `copy_folder` in the Python client and `hops datasource sharepoint`; `hopsworks.variables.enable_sharepoint_storage_connectors` turns them off ([SharePoint][docs-sharepoint]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Hopsworks data sources, which read the feature groups of a project on another Hopsworks cluster through ArrowFlight, as external feature groups or with dltHub ingestion, also with `hops datasource create hopsworks`; `hopsworks.variables.enable_hopsworks_storage_connectors` turns them off ([Hopsworks data source][docs-ds-hopsworks]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added Microsoft SQL Server and Azure SQL Database as SQL data sources, read through Spark, ArrowFlight, Trino catalogs and dltHub ingestion ([SQL Server][docs-ds-sqlserver]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added departments, which group projects: administrators create them and move projects between them in the cluster settings, and `hopsworks.create_project` in the Python client takes a `department`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Changed public (no-login) access to apps to cover every kind of app instead of only Streamlit apps, turned on by `hopsworks.variables.enable_public_apps` ([public access][docs-apps-public]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added a table viewer to the Apps page, and the dropping of an app's tables when the app is deleted, for apps created from 5.2.2 on, which create their tables with `App.create_table` and change them with `App.alter_table` in the Python client ([database access][docs-apps-db]).
- <span class="hops-release-tag">API</span> Added `limit` to `FeatureGroup.read` and `ExternalFeatureGroup.read` in the Python client, and changed limited reads through ArrowFlight, such as `show(n)` and `hops fg preview`, to stop after that many rows instead of reading the whole feature group.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed starting a terminal, notebook, Python job, Python app or deployment without enough project quota being accepted and then failing without a reason; the start is now refused with `Project quota exceeded`, and the Jupyter, Jobs and Deployments pages show the project's quota.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed saving a Python app on a cluster with Kueue failing with `Queues in Kueue are not supported for:PythonApp`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed the Spark properties of a job or a Jupyter server written with spaces around `=`, such as `spark.executor.cores = 2`, being ignored.
- <span class="hops-release-tag">API</span> Fixed `get_feature_groups` without a name in the Python client returning external feature groups and spine groups as managed ones, so `get_external_feature_groups` without a name returned none.
- <span class="hops-release-tag">API</span> Fixed `hops fg create-external` saving the feature group without its `--query` or `--path`, added `--features` and `--online` to it and `--type` to `hops fg list`, and changed the type label of managed feature groups in `hops fg list` and `hops fg info` from `cached` to `managed`.
- <span class="hops-release-tag">API</span> Fixed `hopsworks.login()` and Kafka inserts failing with `FileNotFoundError` on Windows, where the Python client used `/tmp`.
- <span class="hops-release-tag">API</span> Fixed waiting for an online ingestion of no rows in the Python client lasting until its timeout, or forever with `timeout` 0.
- <span class="hops-release-tag">FRONTEND</span> Addressed vulnerabilities in Jupyter, Python apps, the Spark UI and the Ray dashboard.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added **Compute Allocation** to the cluster settings of a cluster with Kueue, where administrators set the compute each department and project is guaranteed and can borrow, which Hopsworks applies as Kueue Cohorts and ClusterQueues, and follow its use.
  It needs `global._hopsworks.kueue.queueModel: per_project`, set at install; moving a running cluster from `shared`, the default, is not supported.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added editing of node labels and priority classes to the **Kubernetes Scheduler** cluster settings, once `hopsworks.rbac.nodeLabels.edit` and `hopsworks.rbac.priorityClasses.edit` grant Hopsworks the permissions, both off by default.
  `hopsworks.rbac.nodeLabels.edit` grants patch on every node, which Kubernetes RBAC can't limit to labels, so it also allows changing taints.
- <span class="hops-release-tag">FRONTEND</span> Changed **Extract** in the file browser to unzip or untar an archive in a background Terminal tab, instead of in a Spark job that copied it to a node's local disk and could fill it; without the Terminal it still runs the job.
- <span class="hops-release-tag">FRONTEND</span> Added the selection of several files in the file browser, by dragging, Ctrl or Cmd-click and Shift-click, and their deletion with **Delete** or the Delete key.
- <span class="hops-release-tag">FRONTEND</span> Added cancelling uploads in the file browser, per file or with **Cancel all**.
- <span class="hops-release-tag">FRONTEND</span> Added sorting a dataset in the file browser by **Last update** and **Author**, and fixed going up to the dataset root after sorting a folder failing with HTTP 404.
- <span class="hops-release-tag">FRONTEND</span> Added a **show shared** switch to the feature group list, which hides the feature groups shared with the project.
- <span class="hops-release-tag">FRONTEND</span> Fixed the **Add members** dialog saying every user is already a member on clusters with user search disabled.
- <span class="hops-release-tag">FRONTEND</span> Fixed the admin **Users** page not scrolling, which left most users out of reach, and added a sortable **Last login** column.
- <span class="hops-release-tag">FRONTEND</span> Fixed error messages that were empty or generic for many failed actions, and 502 and 503 errors showing the raw HTML page of the ingress.
- <span class="hops-release-tag">FRONTEND</span> Fixed a feature store shared with a project, or unshared from it, showing the change only after logging out and in again.
- <span class="hops-release-tag">FRONTEND</span> Fixed a failed Jupyter start showing an exception class name, such as `KubeException`, instead of the reason.
- <span class="hops-release-tag">HELM</span> Fixed Airflow on 5.2.0 and 5.2.1 having no `task_reschedule` table, and the upgrade from 5.1 emptying the Airflow tables after it, such as variables and XComs; the upgrade to 5.2.2 creates the missing table, and a failed conversion now fails the Airflow migrate Job.
  Rows that an earlier upgrade to 5.2.0 or 5.2.1 lost are not restored: re-create them or restore them from a backup.
- <span class="hops-release-tag">HELM</span> Fixed an OpenSearch snapshot restore leaving the `.opendistro_security` index closed, after which OpenSearch crash-loops ([recover a cluster restored on 5.2.0 or 5.2.1][docs-os-restore-crash-loop]).
- <span class="hops-release-tag">HELM</span> Fixed a fresh `helm install --wait` hanging when it ran more than an hour after the Trino auth seed Job finished, because the Job's TTL deleted it; `trino.authSeeder.ttlSecondsAfterFinished` now defaults to `global._hopsworks.jobs.ttlSecondsAfterFinished` (1 day).
- <span class="hops-release-tag">HELM</span> Added support for Kubernetes 1.33, which the chart refused below 1.34.
- <span class="hops-release-tag">HELM</span> Changed the KEDA CRD upgrade hook to read the CRDs from the `hopsworks/keda-crds` image (`hw-keda.crdUpgradeJob.image`) instead of a hook ConfigMap, which took 13% of the 1 MiB Helm release Secret; mirror that image on an airgapped cluster ([KEDA CRDs][docs-keda-crds]).
- <span class="hops-release-tag">HELM</span> Moved the upgrade guide and the Helm 4 migration notes from the chart's `UPGRADE.md` and `HELM4_MIGRATION.md` to the docs ([upgrading Hopsworks][docs-upgrade], [upgrading with Helm 4][docs-helm4]).
- <span class="hops-release-tag">HELM</span> Fixed the Jobs of the VPA install and uninstall, the Kueue objects, the Trino auth seed, the Airflow key bootstrap, Velero backups, the OpenSearch reindex and the wipe ignoring `global._hopsworks.nodeSelector` and `global._hopsworks.tolerations` ([dedicated node pools][docs-node-pools]).
- <span class="hops-release-tag">HELM</span> Fixed the `TooManyShards` alert never firing; a cluster above 90% of its shard limit starts alerting after the upgrade.
- Addressed vulnerabilities in the Hopsworks server image and its dependencies, the UI dependencies and the database migration image.
- Addressed vulnerabilities in the Superset, Prometheus, Grafana, OpenSearch Dashboards, OpenSearch metrics exporter, Logstash and Trino images.
- Addressed vulnerabilities in the Python environment base images and in the Airflow, terminal, Kafka, Vertical Pod Autoscaler, Spark operator CRD, KServe, KEDA HTTP add-on, remote shuffle service init and BuildKit images.
- Fixed the statistics cleaner's query taking an hour or more per run on clusters with many feature statistics, during which it blocked the schema migrations of the statistics tables.
- Fixed the Superset Redis being OOM-killed every few hours by zombie processes its health probes left behind.
- Changed the RonDB REST server to size its buffer pool from its memory limit, return idle buffers after 15 minutes and opt out of transparent huge pages, so that its memory stays bounded.

### Breaking changes

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> OnlineFS now drops a record that did not arrive on the online topic of its feature group, and a project can no longer use the online topic of another project (`TOPIC_IN_USE_BY_ANOTHER_PROJECT`), to address a vulnerability ([what this affects][docs-upgrade-onlinefs]).
  Restart the producers that hold on to a topic after it changes; on a Kafka where records reach OnlineFS on a renamed topic, such as a mirrored secondary, set `onlinefs.configmapOverrides.onlinefssite.service.verifyFeatureGroupTopic: false`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> On a cluster with Kueue, Spark jobs are now queued by Kueue and run with static executors instead of dynamic allocation: a job saved with dynamic allocation runs its dynamic-allocation minimum, at least one executor, and `spark.dynamicAllocation.enabled=true` is refused; set `spark.executor.instances` for more executors ([what changes][docs-kueue-what-changes]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Apps created from 5.2.2 on can no longer create or alter tables in the online feature store database with their own SQL, which the apps of Data Owners could before; create them with `App.create_table` and change them with `App.alter_table` in the Python client, or with `createTable` and `alterTable` of `@hopsworks/app` in Node.js ([database access][docs-apps-db]).
- <span class="hops-release-tag">API</span> Python clients older than 5.2.2 fail to list the data sources of a feature store that has a Hopsworks or SharePoint data source; use the 5.2.2 client in the jobs and environments that list them ([client version][docs-sharepoint-client]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Ingestion from a MongoDB data source, and fetching its schema, now needs MongoDB 4.4 or later instead of 3.6.
- <span class="hops-release-tag">FRONTEND</span> A Jupyter server can no longer be opened through Hopsworks with only its `?token=` URL, to address a vulnerability; open it from the Hopsworks UI.
- <span class="hops-release-tag">FRONTEND</span> Python apps can no longer call Hopsworks with the session of the user who opens them, to address a vulnerability; give an app that calls Hopsworks credentials of its own, such as an API key.
- <span class="hops-release-tag">HELM</span> Hopsworks now uses Kueue 0.20.3 installed as its own release, `kueue` in `kueue-system`, instead of the Kueue 0.12.2 bundled in its namespace, and the chart refuses to render while the bundled Kueue still runs ([Kueue outside the Hopsworks namespace][docs-upgrade-kueue-external]).
  With `global._hopsworks.kueue.enabled: true`, move Kueue with the 5.1 chart's `migrate-to-external.sh` before the upgrade, run the chart's `upgrade-kueue.sh` after it, and remove `hw-kueue.managerConfig`, `hw-kueue.nameOverride` and every `hw-kueue.kueue` value but `enabled` from your values, or the values schema rejects the upgrade.
- <span class="hops-release-tag">HELM</span> Remove `rondb.rondb.rdrs.internal.bufferSize`, `gomaxprocs` and `maxAllocatedBuffers` from your values, which the chart ignored, or `helm upgrade` fails before changing anything.
- <span class="hops-release-tag">HELM</span> The hopsworks, HopsFS and Hive schema migrations now run on the DDL SQL node instead of the application SQL nodes, so they no longer wait for application queries; a cluster that disables it (`rondb.rondb.meta.ddlMySQLd.enabled: false`) sets `hopsworks.dependencies.mysql.ddlConsulServiceName`, `hopsfs.dependencies.mysql.ddlConsulServiceName` and `hive.metastore.dependencies.mysql.ddlConsulServiceName` to `mysql`, or the migrations wait for a SQL node that doesn't exist ([DDL SQL node][docs-upgrade-ddl]).
- <span class="hops-release-tag">HELM</span> The chart package no longer ships `values.aws.yaml`, `values.azure.yml`, `values.gcp.yaml`, `values.rhel8.yaml` and `values.nodeselector-tolerations.yaml`; keep a copy of the one you pass, or take its values from the [AWS][docs-setup-aws] or [Azure][docs-setup-azure] setup guide, [Python build performance][docs-buildkitd-rhel8] or [dedicated node pools][docs-node-pools].

### Deprecations

- <span class="hops-release-tag">HELM</span> Deprecated `hopsworks.variables.streamlit_sharing`; use `hopsworks.variables.enable_public_apps`, which covers every kind of app, and `true` in either one turns public access on.

### Upgrade steps

Follow [Changes from 5.2.2][docs-upgrade-changes-from-522] in the upgrade guide.

### Upgraded components

| Component | 5.2.1 | 5.2.2 |
| --------- | ----- | ----- |
| Airflow image | 5.2.0 | 5.2.1 |
| Alertmanager | 0.34.0-alpine-h1.1 | 0.34.0-alpine-h2.1 |
| ArrowFlight server | 5.2.0 | 5.2.1 |
| Base environment images | 5.2.1 | 5.2.3 |
| BuildKit | v0.32.2 | v0.34.1 |
| Grafana | 12.4.9-h12 | 12.4.13-h0 |
| KEDA HTTP add-on | 0.16.0 | 0.16.0-h2 |
| KServe RBAC proxy | v0.18.0 | v0.23.0 |
| KServe utilities | 0.1.11 | 0.1.12 |
| kube-state-metrics | v2.12.0 | 2.20.0-alpine-h2.0 |
| Logstash | 7.17.29.6 | 7.17.29.7 |
| MySQL Connector/J | 8.0.21.1 | 8.0.21.2 |
| OnlineFS | 2.5 | 2.5.2 |
| OpenSearch Dashboards | 3.8.0.0 | 3.8.0.1 |
| OpenSearch metrics exporter | 1.11.0-alpine-h1.1 | 1.11.0-alpine-h2.1 |
| Payara | 6.2025.11-jdk21.1 | 6.2025.11-jdk21.2 |
| Prometheus | 3.14.0-alpine-h1.1 | 3.15.1-alpine-h2.0 |
| Prometheus adapter | 0.12.0-alpine-h1.1 | 0.12.0-alpine-h2.1 |
| Prometheus config reloader | 0.93.1-alpine-h1.1 | 0.93.1-alpine-h2.1 |
| Prometheus node exporter | 1.12.1-alpine-h1.1 | 1.12.1-alpine-h2.1 |
| Pushgateway | 1.11.3-alpine-h1.1 | 1.11.3-alpine-h2.1 |
| Remote shuffle service init image | 0.9.2 | 0.9.3 |
| RonDB | 26.02.11 | 26.02.12 |
| Superset | 6.0.0p4.5 | 6.0.0p4.6 |
| Superset init image | 0.14.0-alpine-h1.1 | 0.14.0-alpine-h2.1 |
| Superset MySQL | 8.4.11-ubuntu24.04-h5 | 8.4.13-ubuntu24.04-h0 |
| Superset MySQL metrics exporter | 0.20.0-alpine-h1.1 | 0.20.0-alpine-h2.1 |
| Superset MySQL volume permissions image | 12-alpine-h1.1 | 12-alpine-h2.1 |
| Superset Redis | 7.4.11-alpine-h2 | 7.4.11-alpine-h4 |
| Superset Redis metrics exporter | 1.90.0-alpine-h1.1 | 1.90.0-alpine-h2.1 |
| Trino | 483-v2 | 483-v3 |
| Vertical Pod Autoscaler installer | 1.7.1-2 | 1.7.1-3 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed the memory request and limit of the RonDB REST server pods (`rondb.rondb.resources.requests.memory.rdrsMiB`, `rondb.rondb.resources.limits.memory.rdrsMiB`) from 100 MiB and 500 MiB to 2 GiB; check node capacity before the upgrade.
- <span class="hops-release-tag">HELM</span> Changed the default of `hopsworks.variables.agent_deployment_otel_memory_mb`, the memory request and limit of the OTLP sidecar of traced agent deployments, from 1024 to 2048.
  Each traced agent deployment reserves it on its node once it is recreated, so check node memory before you redeploy them ([OTLP sidecar][docs-upgrade-otlp]).
- <span class="hops-release-tag">HELM</span> Removed `hw-kueue.managerConfig`, `hw-kueue.nameOverride` and the `hw-kueue.kueue` values but `enabled`, which configured the Kueue the chart bundled until 5.2.1.
- <span class="hops-release-tag">HELM</span> Removed the Kueue image from `values.airgap.yaml`, because the chart no longer installs Kueue; pass the mirrored image and chart to `upgrade-kueue.sh` with `--kueue-image` and `--kueue-repo` instead.
- <span class="hops-release-tag">HELM</span> Added `global._hopsworks.kueue.namespace` (default `kueue-system`), the namespace of the Kueue release Hopsworks uses.
- <span class="hops-release-tag">HELM</span> Added `hw-kueue.enforcePrerequisiteCheck` (default `true`); `false` skips the check that refuses to render while the bundled Kueue 0.12 or its `v1alpha1` storage is still on the cluster, or while no Kueue serving `v1beta2` is installed.
- <span class="hops-release-tag">HELM</span> Added `onlinefs.configmapOverrides.onlinefssite.service.verifyFeatureGroupTopic` (default `true`), which turns off the OnlineFS check that a record arrived on the online topic of its feature group.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.dependencies.mysql.ddlConsulServiceName`, `hopsfs.dependencies.mysql.ddlConsulServiceName` and `hive.metastore.dependencies.mysql.ddlConsulServiceName` (default `mysqlddl`), the SQL node the schema migrations connect to.
- <span class="hops-release-tag">HELM</span> Added the RonDB REST server settings the chart didn't expose, under `rondb.rondb.rdrs`, such as its buffer pool, its request, BLOB and batch size limits, its rate limit and its use of transparent huge pages.

### Python client

<div class="hops-client-notes" data-versions="5.2.2" markdown>

The release notes of hopsworks-api [5.2.2](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.2.2).

</div>

## 5.2.1 { #hopsworks-5-2-1 }

Helm chart 5.2.1 · Released on 2026-10-16 · Changes since [5.2.0][hopsworks-5-2-0] · Python client [5.2.1](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.2.1), unchanged

### What's changed

- <span class="hops-release-tag">HELM</span> Fixed the 5.1 to 5.2 index pass failing with `field _id not found` on an index with an empty mapping, such as a `projects` index that was never written, and losing documents that were not yet refreshed when it copied an index.
- <span class="hops-release-tag">HELM</span> Addressed vulnerabilities in OpenSearch.
  It applies once an OpenSearch pod logs `Security configuration applied` after the upgrade; if none does, run `run_security_admin.sh` as [Security configuration][docs-os-security] shows.
- <span class="hops-release-tag">HELM</span> Changed the OpenSearch index pass of the 5.1 to 5.2 upgrade to copy one index at a time behind a write block on that index alone, instead of refusing every OpenSearch client, reads included, for the whole pass; upgrade 5.1 clusters to 5.2.1 rather than 5.2.0 ([what keeps working][docs-os-what-works]).
- <span class="hops-release-tag">HELM</span> Fixed OpenSearch restarts and the 5.1 to 5.2 index pass deleting the OpenSearch roles behind restricted grants and feature group shares, which left a shared project with HTTP 403.
  Grants and shares made before the last OpenSearch restart on 5.2.0 lost their roles, and granting or sharing again is refused: revoke and grant again, or unshare and share again.
- <span class="hops-release-tag">HELM</span> Changed the OpenSearch index pass to run before the Kafka migration, so its report (`olk.opensearch.indexUpgrade.mode: report`) and its refusals stop an upgrade from 5.1 before anything changes ([set the timeout][docs-os-timeout]).
- <span class="hops-release-tag">HELM</span> Fixed the OpenSearch index pass not refusing a 5.0 cluster, still on OpenSearch 1.x, which can't be upgraded straight to 5.2.
- <span class="hops-release-tag">HELM</span> Fixed every `helm upgrade` restarting OpenSearch.

### Breaking changes

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Feature groups shared with a subset of their features can no longer be queried in Trino, only read online and in similarity search, to address a vulnerability; share the whole feature group to query it in Trino.
  `hopsworks.variables.trino_feature_group_subset_shares_enabled: "true"` turns Trino access to them back on, which undoes that.
- <span class="hops-release-tag">HELM</span> Remove `olk.opensearch.indexUpgrade.enabled` from your values, using `olk.opensearch.indexUpgrade.mode: report` in place of `false`, or the values schema rejects the upgrade.
- <span class="hops-release-tag">HELM</span> `helm upgrade` now stops before changing anything, also on a cluster already on 5.2.0, if an OpenSearch vector field would still be served by nmslib, which crashes OpenSearch 3.8 nodes on CPUs without AVX-512, where 5.2.0 upgraded without checking; apply the remedy the hook's log gives for each index or template it names ([nmslib fields][docs-os-nmslib]).

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed `olk.opensearch.indexUpgrade.enabled` to `olk.opensearch.indexUpgrade.mode`: `copy`, the default, or `report`, which lists the indices the 5.1 to 5.2 upgrade would copy and how long each stays blocked, and fails before anything changes, the Kafka migration included ([set the timeout][docs-os-timeout]).
  From 5.1.2 the 5.1 chart reads the same key to copy the indices before the 5.2 upgrade; pass it with `--set`, not in your values file ([copying on 5.1][docs-os-copy-51]).
- <span class="hops-release-tag">HELM</span> Added `hopsworks.variables.trino_feature_group_subset_shares_enabled` (default `"false"`), which lets Trino serve feature groups shared with a subset of their features.

### Python client

The Python client is unchanged, 5.2.1.

## 5.2.0 { #hopsworks-5-2-0 }

Helm chart 5.2.0 · Released on 2026-10-15 · Changes since [5.1.0][hopsworks-5-1-0] · Python client 5.2.1 ([5.2.0](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.2.0), [5.2.1](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.2.1))

Upgrade 5.1 clusters, and 5.1.2 clusters in particular, to [5.2.1][hopsworks-5-2-1] instead of 5.2.0, as its breaking changes explain.

### What's changed

- <span class="hops-release-tag">API</span> Fixed, on clusters with more than one RonDB MySQL server, library installs building on an older image of the environment and losing earlier installs, `insert(wait_for_online_ingestion=True)` returning before the rows were online, and apps reporting the execution they replaced.
- <span class="hops-release-tag">API</span> Addressed vulnerabilities in the Airflow, HopsFS, Hive Metastore, Trino, Spark history server and Ray images, and in the Hopsworks server and Java client dependencies.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added the KServe Standard deployment mode, the default for new model deployments, which runs a deployment as a Kubernetes Deployment instead of a Knative service; existing deployments keep Knative mode, and `knative_mode` of a predictor in the Python client chooses the mode.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added KEDA 2.21 and its HTTP add-on, installed by default (`hopsworks.variables.kube_keda_installed`), to autoscale Standard-mode deployments on CPU, memory, requests or vLLM engine metrics and scale them to zero, set with `autoscaler` of a predictor's scaling configuration in the Python client ([scaling][docs-autoscaling], [values][values-hw-keda]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added the Factory, where users build ML systems and analytics tables in the web terminal, with their code in Git or in Hopsworks only; it needs `hopsworks.terminal.enabled` (`hopsworks.factory_enabled`, `hopsworks.factory_chat_enabled`) ([Factory][docs-factory]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Changed job, Jupyter, app and deployment logs to be read live from Kubernetes instead of being shipped to OpenSearch; Jupyter and app logs, and those of Python deployments with disk logging on (the default), are archived to the project's `Logs` dataset, which `Deployment.download_logs` in the Python client downloads for a deployment ([deployment logs][docs-serving-logs]).
- <span class="hops-release-tag">API</span> Added support for Python 3.14 to the Python client.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added real-time feature logging to an online feature group, for feature views that start logging on 5.2 ([feature logging][docs-feature-logging]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added scale to zero for idle Python apps when KEDA is installed, after `hopsworks.variables.pythonapp_idle_cooldown_seconds` (6 hours), which `create_app` in the Python client sets per app with `idle_scale_to_zero` and `idle_cooldown_seconds`; with ingress-nginx, raise `nginx.ingress.kubernetes.io/proxy-read-timeout` in `hopsworks.ingress.annotations`, or the request that wakes an app fails after 60 seconds ([apps][docs-apps-idle]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added database access for apps, on by default (`db_access` of `create_app` in the Python client), which creates the project's online feature store database and gives the app its `MYSQL_*` connection variables; apps also get `TRINO_*` variables where the project has Trino ([database access][docs-apps-db]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added Flask, Gradio and Node.js apps (`app_kind` of `create_app` in the Python client), which start from their app file like Streamlit apps ([app types][docs-apps-types]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added versions of model deployments, so an earlier configuration can be activated again, in the Python client with `Deployment.rollback` ([versions][docs-deployment-versions]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added deployment schemas, which validate inference requests, and deployments of a model or a feature view without a predictor script ([deployment schema][docs-deployment-schema]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added separate Python environments for the predictor and the transformer of a model deployment (`environment` of `Transformer` in the Python client).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added per-member credentials for Oracle data sources, so that each member reads with their own username, password and wallet, also through Trino catalogs and in jobs, Jupyter, terminals and apps ([provided credentials][docs-ds-provided]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added event-time windows to feature monitoring, the default for new configurations on feature groups and feature views with an event time; existing configurations are unchanged ([time basis][docs-fm-time-basis]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added `spine_df` to `FeatureView.get_batch_data` and the training data methods, to read features for rows you supply, within `hopsworks.variables.featurestore_asof_spine_max_rows` and the related size limits.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed removing a member from a project to hand their home directory to a Data Owner, chosen in the remove dialog or with `new_file_owner` of `Project.remove_member` in the Python client, instead of leaving it unreadable ([removed members][docs-member-files]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed project creation to give every project Trino and Superset where the cluster runs them, also through the REST API, where a request without a service list created a project with Kafka only.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added the `TERMINAL` API key scope, so that the `hops` CLI can start, extend and attach to web terminal sessions and teleport Claude Code sessions to them; staged teleport files are deleted after `hopsworks.terminal.teleportTtlDays` (7), and the unused `KUBE` and `SINK` scopes are removed ([teleport][docs-teleport], [scopes][docs-api-scopes]).
- <span class="hops-release-tag">API</span> Added the Hopsworks skills for coding agents to the Python client package, so that `hops skills install` adds them to a repository on your own machine ([coding agents][docs-agent-skills]).
- <span class="hops-release-tag">API</span> Added `hops logout`, and changed `hops setup` to ask for the host again, so that it can move to another cluster, and to write `AGENTS.md` and `.claude/` files for coding agents into the current directory unless given `--no-scaffold`.
- <span class="hops-release-tag">API</span> Changed `hops datasource create` to create every data source type.
- <span class="hops-release-tag">API</span> Changed the `hops` CLI to explain a failed request, such as one with an API key that lacks a scope, instead of printing a traceback, and `hops project list` to list every project you are a member of instead of only the active one.
- <span class="hops-release-tag">API</span> Changed reads of a feature view whose feature groups all come from one Snowflake data source to run the point-in-time joins in Snowflake.
- <span class="hops-release-tag">API</span> Changed online feature vector reads and model deployment predictions in the Python client to make fewer round trips, and concurrent reads through one feature view to run in parallel instead of one at a time; `Deployment.init_predict` prepares a deployment's first prediction ahead of it.
- <span class="hops-release-tag">API</span> Changed `import hopsworks` and `hopsworks.login()` in the Python client to take a fraction of the time, which also speeds up every `hops` command.
- <span class="hops-release-tag">API</span> Added `timeout` to `FeatureView.get_feature_vector` and `get_feature_vectors` in the Python client, a deadline for the whole online read.
- <span class="hops-release-tag">API</span> Added `Query.read_batches` to the Python client, which reads a query as Arrow record batches instead of loading the whole result.
- <span class="hops-release-tag">API</span> Added `ModelServing.get_vllm_image_tags` to the Python client, which lists the vLLM image tags an LLM deployment can use.
- <span class="hops-release-tag">API</span> Changed `FeatureGroup.remove_rows` in the Python client to also delete the online rows of a feature group with an embedding index from the vector database, where it deleted only the offline rows.
- <span class="hops-release-tag">API</span> Changed the first offline materialization of a feature group written from Spark to start at the feature group's creation instead of the oldest record of the project's online topic; the `initial_offset_margin_hours` write option (1 by default) moves the start earlier to allow for clock skew.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added npm installs of many packages in one build, from a list, a `package.json` with its lockfile or a Git repository, in the Python client with `Environment.install_npm`, `install_npm_from_package_json` and `install_npm_from_git` ([npm packages][docs-npm]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Elasticsearch, ClickHouse and Teradata as data sources.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added a `hopsworks_analytics` project, off by default (`hopsworks.variables.hopsworks_analytics`), for cluster-wide Superset dashboards on the Hopsworks metadata, which exposes user email addresses to every Hopsworks administrator and to the model provider of the dashboard setup ([values][values-hopsworks-analytics]).
- <span class="hops-release-tag">API</span> Changed Hugging Face model imports to download a single commit, which can be given as a revision and is recorded in the model's description.
- <span class="hops-release-tag">API</span> Changed the Python client to accept opensearch-py 3.
- <span class="hops-release-tag">API</span> Fixed `find_neighbors` in the Python client failing with `Engine [NMSLIB] does not support filters` on embedding feature groups created before 5.1, and failing on a project's default embedding index when fewer rows than `k` match (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">API</span> Fixed external Spark clients failing to read, write or delete Delta feature groups on clusters without the HopsFS NameNode and DataNode load balancers (also in [5.1.2][hopsworks-5-1-2]).
- <span class="hops-release-tag">API</span> Fixed `Deployment.start()` and `save()` in the Python client returning before the deployment could be reached, so that the first `predict()` failed with "Deployment not created or running".
- <span class="hops-release-tag">API</span> Fixed `get_feature_vectors` in the Python client failing a whole batch with HTTP 400 through the REST client on an entry with a null primary key, which the SQL client served.
- <span class="hops-release-tag">API</span> Fixed the first insert from the Python engine into a feature group whose Kafka topic had just been created failing with `NOT_LEADER_FOR_PARTITION`.
- <span class="hops-release-tag">API</span> Fixed inserts from the Python engine failing on a timestamp column holding a null (`NaT`).
- <span class="hops-release-tag">API</span> Fixed Kafka clients started at the same time in one Python client process, such as two streaming pipelines on two threads, failing with `ssl.ca.location failed` while another rewrote the certificate files they share.
- <span class="hops-release-tag">API</span> Fixed `DataSource.infer_metadata` in the Python client failing with `AttributeError: 'dict' object has no attribute 'name'`.
- <span class="hops-release-tag">API</span> Fixed the Python client installed with its `python` or `polars` extra failing to import pyarrow on NumPy 1 (also in [5.1.2][hopsworks-5-1-2]).
- <span class="hops-release-tag">API</span> Fixed `hops sql` failing with `Schema '<project>' does not exist` unless given a schema; it now reads the project's feature store in the `delta` catalog by default, so pass `--catalog iceberg` for Iceberg feature groups.
- <span class="hops-release-tag">API</span> Fixed `hops setup` failing on a machine with PySpark installed.
- <span class="hops-release-tag">API</span> Fixed `create_feature_view` not warning, and `get_feature_vector` giving a misleading error, when a feature view selects the time partition columns of a Hudi feature group, which are offline only.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Addressed vulnerabilities in jobs (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">API</span> Fixed model deployments answering 403 to users whose password an administrator set.
  Such users already on a temporary password regain access at their next password change or account update.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed the built-in Python environments to Python 3.13; custom environments keep the Python version they were built with.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed datasets to be searchable by name, description and tags; tags now attach to a whole dataset, and existing tags on files inside a dataset can be read and deleted but no longer added.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">HELM</span> Added Trino connector table functions, such as `system.query`, for Data Owners on their project's Trino catalogs.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Changed adding a member to a project to create the member's home directory in the background.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed project creation to refuse a pre-created namespace longer than 25 characters, like a project name.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">HELM</span> Fixed filtered similarity search failing on embedding indices created before 5.1, which the upgrade rebuilds on faiss ([upgrading OpenSearch][docs-os-faiss]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed ingestion statistics runs piling up on frequently written feature groups and leaving other jobs pending; at most one runs per feature group.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed deleted feature views, feature groups and projects coming back as empty entries, which blocked creating new ones with the same name.
- <span class="hops-release-tag">API</span> Fixed Python engine reads failing for queries with a filter on a joined feature group or a filter that compares features of two feature groups.
- <span class="hops-release-tag">API</span> Fixed writes to Iceberg feature groups from Python jobs and apps failing with `Unable to load libhdfs`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed a killed Spark job with pending executors staying in the aggregating logs state for up to 20 minutes (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed project creation sometimes failing and rolling back on a group that "already exists".
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed tags and keywords added right after an artifact was created missing from search.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed LLM deployments to vLLM and vLLM-Omni v0.28.0; deployments created or edited on 5.1 keep v0.20.0 until you change their vLLM version, so keep the v0.20.0 images in a mirrored registry.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed Trino giving a project access to only one of several feature stores shared with it (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed Git operations failing with an I/O error when a Git provider proxy is set and HopsFS encryption at rest is on.
- <span class="hops-release-tag">API</span> Fixed ArrowFlight reads of PostgreSQL `uuid` columns.
- <span class="hops-release-tag">API</span> Fixed ArrowFlight queries of PostgreSQL data sources run by a read-only role.
- <span class="hops-release-tag">API</span> Fixed ArrowFlight reads of MySQL data sources without a password.
- <span class="hops-release-tag">API</span> Fixed ArrowFlight reads from S3 and AWS Glue data sources on OpenShift.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Addressed vulnerabilities in model deployments.
- <span class="hops-release-tag">FRONTEND</span> Fixed queued service operations failing on every retry when they removed a user from Superset or set up Superset for a project Observer (also in [5.1.2][hopsworks-5-1-2]).
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Changed Trino to read its users, groups, user catalogs and access rules from HopsFS instead of Secrets, which fixes `helm upgrade` failing on clusters with many Trino catalogs or project members.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Fixed the post-upgrade search index rebuild failing without the admin password, for example when password login is disabled, and running on every upgrade; it now runs in the background once per index format, and shows its progress and a **Reindex now** button under **Cluster settings > Service Operations > OpenSearch Index Commands** (also in [5.1.1][hopsworks-5-1-1]).
  An upgrade from 5.1.0 rebuilds the index, and search is incomplete until it finishes.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added sharing of Trino catalogs and feature groups with other projects, down to schemas, tables and columns, and private Trino catalogs of one user ([sharing][docs-trino-sharing], [private catalogs][docs-trino-private]).
- <span class="hops-release-tag">FRONTEND</span> Changed the feature store sharing card in the project settings to manage shares with projects and grants to restricted users, of the whole feature store or of single feature groups ([sharing][docs-fs-sharing]).
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added a memory guard to web terminals, on by default (`hopsworks.terminal.oomGuard`), which ends the process using the most memory instead of losing the session, and an out-of-memory notice when a session is lost anyway.
- <span class="hops-release-tag">FRONTEND</span> Changed the Jupyter overview to show the server's log archives and its configuration, which is saved when you start Jupyter.
- <span class="hops-release-tag">FRONTEND</span> Fixed a Jupyter server that had just started failing requests on clusters with more than one Hopsworks instance.
- <span class="hops-release-tag">FRONTEND</span> Fixed Spark Connect in web terminals never starting on clusters that enforce the Kyverno restricted policies.
- <span class="hops-release-tag">FRONTEND</span> Fixed the table preview of a PostgreSQL data source failing for a role granted only some columns.
- <span class="hops-release-tag">FRONTEND</span> Added time-to-live settings to the feature group create and edit forms, which ask before a change deletes online rows ([TTL][docs-ttl]).
- <span class="hops-release-tag">FRONTEND</span> Added an execution graph of the dbt models and tests to the executions of jobs on `dbt-pipeline` environments whose run writes one.
- <span class="hops-release-tag">FRONTEND</span> Changed the SQL Runner of the Query Engine page to fill the page, show row numbers and column types, list a table's columns and draw `EXPLAIN (FORMAT GRAPHVIZ)` plans as a graph, and fixed it leaving queries running and completing no column names after `FROM` (also in [5.1.2][hopsworks-5-1-2]).
- <span class="hops-release-tag">FRONTEND</span> Changed the Set up data source dialog to list each SQL engine and CRM vendor as its own choice, with a filter box, and the Snowflake form to accept a programmatic access token in its password field.
- <span class="hops-release-tag">FRONTEND</span> Fixed the **Why?** button of an online ingestion in a feature group's activity opening every OnlineFS log of the project instead of that ingestion's; it is now named **Logs** (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">FRONTEND</span> Fixed the dialogs of Git repository actions in the project settings closing on their own, and transformation function previews showing the imports of the defining module.
- <span class="hops-release-tag">FRONTEND</span> Changed the coding agent instruction files in members' home directories, such as `AGENTS.md`, to be updated on upgrade unless the member edited them.
- <span class="hops-release-tag">HELM</span> Added the HopsFS CSI driver, on by default (`global._hopsworks.csi.enabled`), which mounts HopsFS into Jupyter, web terminal, Python and Ray job, Python app, Python deployment, Airflow and Trino pods without a privileged container; Trino needs it ([values][values-hopsfs-csi]).
- <span class="hops-release-tag">HELM</span> Added proxy tokens for apps and Superset, off by default (`hopsworks.variables.hopsable_proxy_enabled`), so that a gateway outside the cluster can relay requests to them for API-key clients.
- <span class="hops-release-tag">HELM</span> Fixed agent deployments that mount HopsFS never starting on OpenShift.
- <span class="hops-release-tag">HELM</span> Fixed OpenSearch losing unflushed vectors and rejecting vector writes for about two minutes on its first start on 5.1 (also in [5.1.1][hopsworks-5-1-1]).
  A values file that sets `triggered: true` or `percent: 0.75`, or an upgrade with `--reuse-values`, keeps the problem.
- <span class="hops-release-tag">HELM</span> Fixed RonDB daemons ignoring SIGTERM, so every pod stop ended in a kill and a stopped data node could stall the cluster (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Changed the RonDB REST server probes to outlast a data node failure and to use their own port (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Fixed the RonDB REST server reading request bodies over 64 KiB as empty; it now buffers them in `rondb.rondb.rdrs.uploadPath` (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Fixed Helm 4 server-side apply conflicts on `helm upgrade` over the replicas of HPA-managed workloads other than the RonDB REST server, the rules of the Knative serving webhooks and the `rss-configuration` ConfigMap (see `HELM4_MIGRATION.md` in the 5.2 chart).
- <span class="hops-release-tag">HELM</span> Added support for Kubernetes 1.37; upgrade Hopsworks to 5.2 before the cluster, because 5.1 doesn't install on 1.37.
  Under ArgoCD 3.4 or older with server-side apply, upgrade ArgoCD to 3.5 or ignore `spec.preventPodSchedulingIfMissing` on CSIDrivers before moving to 1.37, or ArgoCD stops syncing (see `UPGRADE.md` in the 5.2 chart).
- <span class="hops-release-tag">HELM</span> Changed the Trino administrator, which the admin SQL console uses, to read only the `system`, `tpch` and `tpcds` catalogs.
- <span class="hops-release-tag">HELM</span> Added backups and restores of the Superset database, on by default with the cluster backups ([disaster recovery][docs-superset-backup]).
- <span class="hops-release-tag">HELM</span> Fixed environment builds never starting when `rondb.rondb.priorityClass` is renamed (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Fixed the Hopsworks admin pod starting before the database migrations had finished.
- <span class="hops-release-tag">HELM</span> Fixed the Hive Metastore `metastore-db-init` Job failing on a fresh install when it started before the HopsFS NameNode.
- Addressed vulnerabilities in the Payara, Hopsworks certificate authority, Grafana, Filebeat and Superset images and the MySQL and Redis of Superset (also in [5.1.2][hopsworks-5-1-2]).

### Breaking changes

- <span class="hops-release-tag">API</span> `get_batch_data` and the training data methods of `FeatureView` in the Python client now raise `TypeError` on a keyword argument they don't take, which they used to ignore; remove such arguments.
- <span class="hops-release-tag">API</span> With `allow_missing=True`, `get_feature_vector` and `get_feature_vectors` in the Python client now return a serving key you pass in its slot of the vector when no row matches it, instead of `None`; code that detects a missing row by a `None` key must check the other features instead.
- <span class="hops-release-tag">API</span> `hops search ls` is now `hops search`, which also finds jobs, apps, models, deployments and agents; update scripts that call `hops search ls`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> MongoDB data sources need MongoDB 4.2 or later; upgrade older MongoDB servers, or browsing and testing their data sources fails.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Web terminal sessions now last 8 hours instead of 4, and every upgrade sets `terminal_session_hours` from `hopsworks.terminal.sessionHours`, replacing a value set in the admin UI, which upgrades used to keep (see `UPGRADE.md` in the 5.2 chart).
- <span class="hops-release-tag">HELM</span> Project namespaces now get a NetworkPolicy that admits traffic only from the same namespace and the Hopsworks namespace, where 5.1 restricted only web terminal pods; add any other namespace that reaches project pods, such as Istio or monitoring, to `hopsworks.variables.project_namespace_network_policy_allowed_namespaces` ([values][values-netpol]).
  Turning them off with `hopsworks.variables.project_namespace_network_policy_enabled: false` leaves the existing ones; delete them with `kubectl delete networkpolicy -A --field-selector metadata.name=hopsworks-project-isolation`.
- <span class="hops-release-tag">HELM</span> Trino no longer records the admin query history, and its records lose their query text after `hopsworks.variables.trino_events_strip_details_after_days` (7) instead of keeping it for 61 days; to keep either, or if your values restate `trino.trino.additionalConfigProperties`, see `UPGRADE.md` in the 5.2 chart (also in [5.1.2][hopsworks-5-1-2]).
- <span class="hops-release-tag">HELM</span> Upgrade to 5.2 only from 5.1, satellites before central, with central and its satellites all under Helm or all under ArgoCD (see `UPGRADE.md` in the 5.2 chart).
- <span class="hops-release-tag">HELM</span> Upgrade 5.1 clusters to [5.2.1][hopsworks-5-2-1] instead of 5.2.0, whose OpenSearch index pass refuses every client and whose OpenSearch restarts delete the roles behind restricted-access grants and feature group shares; on [5.1.2][hopsworks-5-1-2], which fixed those restarts, 5.2.0 is a regression.
- <span class="hops-release-tag">HELM</span> The upgrade migrates Kafka from ZooKeeper to KRaft, which can't be undone: give `helm upgrade` a long enough `--timeout`, never pass `--atomic` (`--rollback-on-failure` in Helm 4) or run `helm rollback`, and under ArgoCD sync twice ([Kafka 4 and KRaft][docs-kafka4-expect]).
- <span class="hops-release-tag">HELM</span> On a cluster installed before 5.1, OpenSearch refuses every client while the upgrade copies its oldest indices; plan the outage and the timeout ([upgrading OpenSearch][docs-os]).
- <span class="hops-release-tag">HELM</span> OpenSearch snapshot repositories you configure yourself under `olk.opensearch.backup.repositories` or `olk.opensearch.restore.repositories`, on MinIO before `RELEASE.2025-01-20T14-49-07Z` or other S3-compatible stores that require `Content-MD5`, which OpenSearch 2.19 sent and 3.8 doesn't, need `legacy_md5_checksum_calculation: true` in their `s3` block, or the upgrade fails; the chart already sets it on the repositories it generates for the bundled MinIO ([upgrading OpenSearch][docs-os]).
- <span class="hops-release-tag">HELM</span> The chart now creates a cluster-wide IngressClass named `istio`, which 5.1 didn't; if the cluster already has one, set `kserve.istio.ingressClass.enabled: false`, or the upgrade fails.
- <span class="hops-release-tag">HELM</span> KEDA, installed by default where 5.1 didn't install it, can't share a Kubernetes cluster with another KEDA or the OpenShift Custom Metrics Autoscaler, including the KEDA of another Hopsworks release such as a satellite; set `hopsworks.variables.kube_keda_installed: false` on all but one (see `UPGRADE.md` in the 5.2 chart).
- <span class="hops-release-tag">HELM</span> With `hopsworks.rbac.useExistingRole: true`, add the chart's new ClusterRole grants (`charts/hopsworks/templates/role.yaml`) on `scaledobjects`, `interceptorroutes`, `ingresses`, `networkpolicies` and `csidrivers`, or model deployments, project creation and every pod that mounts HopsFS fail.
- <span class="hops-release-tag">HELM</span> On OpenShift, use the `values.openshift.yaml` of the 5.2 chart, which creates an SCC for the privileged HopsFS CSI node plugin, new in 5.2; if a cluster administrator owns SCCs, see `charts/hopsfs-csi/README.md` in the chart.
- <span class="hops-release-tag">HELM</span> The post-upgrade search index rebuild signs in as the agent user, instead of with `hopsworks.variables.admin_email` and `admin_password`, and fails the `helm upgrade` if `hopsworks.agent_email` and `hopsworks.agent_password` (default `agent@hops.io`, `admin`) don't match that account (also in [5.1.1][hopsworks-5-1-1]).
  Set them, or turn the rebuild off with `hopsworks.opensearchReindex.enabled: false`.
- <span class="hops-release-tag">HELM</span> The RonDB REST server now uses a probe port and an upload path that need RonDB 26.02.11, where 5.1.0 probed its REST port and set no upload path (also in [5.1.1][hopsworks-5-1-1]).
  If you pin `rondb.rondb.images.rondb.tag` to an older version, set `rondb.rondb.rdrs.probePort.enabled: false` and `rondb.rondb.rdrs.uploadPath: ""`, or the REST server doesn't start.
- <span class="hops-release-tag">HELM</span> Overrides of the Trino `initContainers`, `additionalVolumes` or `additionalVolumeMounts`, including those [5.1.0][hopsworks-5-1-0] asked for with `hopsworks.hopsfsMount.enabled: false`, fail the render; remove the overrides, or copy the new entries from the chart into them.
- <span class="hops-release-tag">HELM</span> If you override `trino.trino.image.tag`, use a Hopsworks Trino image of `483-v2` or later instead of the `480` images of 5.1, and set `global._hopsworks.trino.image.tag` to the same tag.
- <span class="hops-release-tag">HELM</span> If you set `trino.trino.serviceAccount.name`, set `trino.trinotest.serviceAccount.name` to the same name, because the test coordinator now shares the Trino account instead of creating its own `hopsworks-trino-test`, or the chart fails to render.
- <span class="hops-release-tag">HELM</span> Under ArgoCD, or with any `global._hopsworks.mode` other than `auto`, set `trino.auth.adminUserPwd` and `trino.auth.monitoringUserPwd`, which now default to empty instead of fixed passwords, or the chart fails to render.
- <span class="hops-release-tag">HELM</span> Removed the KServe local model controller; if you set `kserve.kserve.localmodel.enabled: true`, remove it, or the chart fails to render.
- <span class="hops-release-tag">HELM</span> With cluster backups on, Superset backups, new in 5.2, need an object store and, with `superset.auth.createSecrets: false`, the `backup.hops.works/include=true` label on its Secrets, or the chart fails to render; set `superset.backups.enabled: false` to skip them ([disaster recovery][docs-superset-backup]).
- <span class="hops-release-tag">HELM</span> If your release namespace is longer than 34 characters, set `global._hopsworks.csi.driverName`, because the default name of the HopsFS CSI driver, new in 5.2, would exceed 63 characters.
- <span class="hops-release-tag">HELM</span> With Helm 4, pass `--skip-crds` to a `helm install` on a cluster that already has the Strimzi CRDs, and after a reinstall run `helm upgrade` once to recreate the KEDA CRDs (see `UPGRADE.md` in the 5.2 chart).
- <span class="hops-release-tag">HELM</span> Kafka 4.3 rejects clients older than Apache Kafka 2.1 or librdkafka 1.8.2, which the Kafka 3.9 of 5.1 accepted; upgrade them first; with your own Kafka cluster, its brokers must run Kafka 2.1 or later ([client compatibility][docs-kafka4-clients]).
- <span class="hops-release-tag">HELM</span> OpenSearch now reports its own version, `3.8.0`, instead of `7.10.2`; check clients that connect to it directly against the [OpenSearch 3.0 breaking changes](https://docs.opensearch.org/latest/breaking-changes/).

### Deprecations

- <span class="hops-release-tag">API</span> Deprecated `entry` of `get_feature_vector`, `get_feature_vectors`, `get_inference_helper` and `get_inference_helpers` in the Python client; use `serving_keys`.
- <span class="hops-release-tag">API</span> Deprecated `Deployment.environment` in the Python client; use `predictor.environment` and `transformer.environment`, because setting it now moves only the predictor, where it used to move the transformer too.
- <span class="hops-release-tag">API</span> Deprecated `source="opensearch"` of `Deployment.read_logs` and `tail_logs` in the Python client, whose default is now `"kubernetes"` instead of `"opensearch"`.
- <span class="hops-release-tag">API</span> Deprecated spine groups (`get_or_create_spine_group`) and the `spine` argument of `FeatureView` reads in the Python client; use `spine_df`.
- <span class="hops-release-tag">API</span> Deprecated `hops init`; use `hops skills install`.
- <span class="hops-release-tag">API</span> Deprecated the `environmentDTO` field of model deployments in the REST API; use `predictorEnvironment` and `transformerEnvironment`.
- <span class="hops-release-tag">HELM</span> Deprecated the name of `kafka.cluster.zookeeper`, which now sizes and places the KRaft controllers; its `services.client.annotations` is no longer read.
- <span class="hops-release-tag">HELM</span> Deprecated `hopsworks.variables.mount_hopsfs_in_python_job`, which only legacy Python jobs still read.
- <span class="hops-release-tag">HELM</span> Deprecated `global._hopsworks.trino.mountableSecrets.mechanism`, `global._hopsworks.trino.userCatalogShards`, `trino.auth.adminUserPwdHash`, `trino.auth.monitoringUserPwdHash` and `hw-kyverno.policyExceptions.trino`, which nothing reads any more.

### Upgrade steps

Follow [Before every upgrade][docs-upgrade-before] and [5.1.x to 5.2.x][docs-upgrade-51-to-52] in the upgrade guide.

### Upgraded components

| Component | 5.1.0 | 5.2.0 |
| --------- | ----- | ----- |
| Airflow image | 5.1.0 | 5.2.0 |
| ArrowFlight server | 5.1.0 | 5.2.0 |
| Base environment images | 5.1.0 | 5.2.1 |
| Filebeat | 8.19.21 | 8.19.23 (also in [5.1.2][hopsworks-5-1-2]) |
| Git image | 1.5 | 1.6 |
| Grafana | 12.4.9-h9 | 12.4.9-h12 |
| Hive Metastore | 4.1.0.0-v1 | 4.1.0.1-v1 |
| HopsFS | 3.4.3.3-EE-RC0 | 3.4.3.3-EE-RC1 |
| Inference logger | 5.1.0 | 5.2.0 |
| Kafka | 3.9.2 | 4.3.1 |
| KServe | v0.19.0 | v0.21.0 |
| KServe utilities | 0.1.10 | 0.1.11 |
| Model serving authenticator | 5.1.0 | 5.2.0 |
| Model serving webhook | 5.1.0 | 5.2.0 |
| OnlineFS | 2.4 | 2.5 |
| OpenSearch | 2.19.6.3 | 3.8.0.0 |
| OpenSearch Dashboards | 2.19.6.7 | 3.8.0.0 |
| Payara | 6.2025.11-jdk21.0 | 6.2025.11-jdk21.1 (also in [5.1.2][hopsworks-5-1-2]) |
| Python in the base environments | 3.12 | 3.13 |
| Ray | 2.55.1 | 2.58.0 |
| RonDB | 26.02.10 | 26.02.11 (also in [5.1.1][hopsworks-5-1-1]) |
| scikit-learn model server | 0.19.1 | 0.21.0 |
| Spark history server | 4.1.3.0 | 4.1.3.1 |
| Storage initializer | 5.1.0 | 5.2.0 |
| Strimzi | 0.45.2-h1 | 1.2.0 |
| Superset | 6.0.0p4.4 | 6.0.0p4.5 (also in [5.1.2][hopsworks-5-1-2]) |
| Superset MySQL | 8.4.11-ubuntu24.04-h4 | 8.4.11-ubuntu24.04-h5 (also in [5.1.2][hopsworks-5-1-2]) |
| Superset Redis | 7.4.11-alpine-h1 | 7.4.11-alpine-h2 (also in [5.1.2][hopsworks-5-1-2]) |
| Toolbox image (hwutils) | 1.9 | 1.10 |
| Trino | 480-v10 | 483-v2 |
| Trino mountable-secrets sidecar | 3.4.3.3-EE-RC0-1 | 3.4.3.3-EE-RC1-1 |
| vLLM | v0.20.0 | v0.28.0 |
| vLLM-Omni | v0.20.0 | v0.28.0 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed the defaults of `olk.opensearch.knn.circuit_breaker.triggered` to `false` and `olk.opensearch.knn.circuit_breaker.percent` to `75` (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Changed the default of `hopsworks.variables.docker_operations_buildkit_priority_class` from `rondb-high-priority` to empty, so environment builds run at the default priority (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Changed `rondb.rondb.terminationGracePeriodSeconds` to one value per component, and the data nodes' default from 60 to 300 seconds; an integer still sets only the data nodes (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Changed `hopsworks.variables.trino_default_catalog` from `hive` to `delta`, the catalog new Superset connections to Trino open in.
- <span class="hops-release-tag">HELM</span> Changed the default driver memory of new Ray jobs (`hopsworks.variables.ray_job_driver_memory`) from 2048 to 4096 MB.
- <span class="hops-release-tag">HELM</span> Changed `values.openshift.yaml` to turn on credential files for Trino catalogs (`global._hopsworks.trino.mountableSecrets.enabled`).
- <span class="hops-release-tag">HELM</span> Changed `hopsworks.variables.trino_events_cleaner_batch_size` from 1000 to 100 (also in [5.1.2][hopsworks-5-1-2]).
- <span class="hops-release-tag">HELM</span> Changed `trino.trinotest.serviceAccount.create` to `false` and `name` to `hopsworks-trino`, so the test coordinator shares the Trino account.
- <span class="hops-release-tag">HELM</span> Changed the defaults of `trino.auth.adminUserPwd` and `trino.auth.monitoringUserPwd` to empty, so the chart generates the passwords.
- <span class="hops-release-tag">HELM</span> Fixed `values.openshift.yaml` for the OpenShift `restricted-v2` SCC, covering Strimzi, the Kafka brokers and KRaft controllers, the Spark operator and the MySQL and Redis of Superset, and turned off the Prometheus node exporter, which OpenShift ships itself (also in [5.1.1][hopsworks-5-1-1]).
  Use it with Helm 4 or Helm 3.17.1 or later, because older Helm 3 releases ignore its `null` overrides of subchart values.
- <span class="hops-release-tag">HELM</span> Fixed `global._hopsworks.jobs.ttlSecondsAfterFinished: 0` being replaced by `86400`.
- <span class="hops-release-tag">HELM</span> Fixed Helm 4 rejecting an empty `kafka.cluster.kafka.authorizer.authorizerClass`.
- <span class="hops-release-tag">HELM</span> Fixed the values schema rejecting `hopsworks.variables.loadbalancer_external_domain_namenode` and `hopsworks.variables.loadbalancer_external_domain_datanode` (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Added `hopsworks.opensearchReindex.enabled` to turn off the post-upgrade search index rebuild, and `hopsworks.variables.command_search_fs_reindex_queue_wait_as_ms` to bound its wait for the search command queue (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Added `kafka.cluster.kafka.config.replicationFactor` and `minInsyncReplicas` for multi-broker Kafka; `minInsyncReplicas` above a topic's replicas makes `acks=all` writes to it fail ([values][values-kafka]).
- <span class="hops-release-tag">HELM</span> Added `nodePort` values to pin the node ports of unmanaged external load balancers: ArrowFlight, OpenSearch, the HopsFS NameNode and DataNodes, Trino, the Hive Metastore, Kafka (`bootstrapNodePort`, `startingNodePort`) and the RonDB MySQL and REST servers (also in [5.1.1][hopsworks-5-1-1]).
  The RonDB ones ignore `global._hopsworks.externalLoadBalancers.managed` and apply only when `rondb.rondb.meta.mysqld.externalLoadBalancer.managed` or `rondb.rondb.meta.rdrs.externalLoadBalancer.managed` is `false`.
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.rdrs.probes` and `rondb.rondb.rdrs.probePort`, the settings and the port of the RonDB REST server probes, and `rondb.rondb.rdrs.maxKeepaliveRequests` to close a keep-alive connection after that many requests, so clients rebalance after a rolling restart (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.rdrs.ttlPurge.enable` and `activeWindow` to turn off the TTL purge of the RonDB REST server or limit it to a daily window (also in [5.1.1][hopsworks-5-1-1]).
- <span class="hops-release-tag">HELM</span> Added `kserve.kserve.storage.resources` for the storage initializer that downloads models into deployment pods.
- <span class="hops-release-tag">HELM</span> Added `kserve.istio.ingressClass.enabled` and `kserve.istio.ingressClass.name` for the cluster-wide IngressClass the chart creates.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.variables.kube_keda_installed` (default `true`), which installs KEDA and lets model deployments autoscale through it.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.variables.project_namespace_network_policy_enabled` (default `true`) and `project_namespace_network_policy_allowed_namespaces` for the NetworkPolicy of project namespaces.
- <span class="hops-release-tag">HELM</span> Added `superset.backups.enabled` (default `true`) for the Superset backups.
- <span class="hops-release-tag">HELM</span> Added `global._hopsworks.csi.driverName` to name the HopsFS CSI driver of a release.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.terminal.sessionHours` (default `8`), which every upgrade writes to the web terminal session length.

[docs-os-what-works]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade_opensearch/#opensearch-what-keeps-working
[docs-os-timeout]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade_opensearch/#opensearch-set-the-timeout
[docs-os-copy-51]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade_opensearch/#opensearch-copy-on-51
[docs-os-nmslib]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade_opensearch/#opensearch-nmslib-fields
[docs-autoscaling]: https://docs.hopsworks.ai/5.2/user_guides/mlops/serving/autoscaling/
[docs-apps-idle]: https://docs.hopsworks.ai/5.2/user_guides/projects/apps/#scale-to-zero-when-idle
[docs-apps-types]: https://docs.hopsworks.ai/5.2/user_guides/projects/apps/#app-types
[docs-apps-db]: https://docs.hopsworks.ai/5.2/user_guides/projects/apps/#database-and-feature-store-access
[docs-npm]: https://docs.hopsworks.ai/5.2/user_guides/projects/python/python_install/#npm-packages
[docs-deployment-versions]: https://docs.hopsworks.ai/5.2/user_guides/mlops/serving/deployment-versions/
[docs-deployment-schema]: https://docs.hopsworks.ai/5.2/user_guides/mlops/serving/deployment-schema/
[docs-fm-time-basis]: https://docs.hopsworks.ai/5.2/user_guides/fs/feature_monitoring/scheduled_statistics/#time-basis
[docs-ds-provided]: https://docs.hopsworks.ai/5.2/user_guides/fs/data_source/creation/sql/#data-source-provided-credentials
[docs-factory]: https://docs.hopsworks.ai/5.2/user_guides/client_installation/#build-an-ml-system
[docs-agent-skills]: https://docs.hopsworks.ai/5.2/user_guides/client_installation/#skills-and-instructions-for-coding-agents
[docs-api-scopes]: https://docs.hopsworks.ai/5.2/user_guides/projects/api_key/api_key_scopes/
[docs-ttl]: https://docs.hopsworks.ai/5.2/user_guides/fs/feature_group/ttl/
[docs-member-files]: https://docs.hopsworks.ai/5.2/user_guides/projects/project/manage_members/#what-happens-to-a-removed-members-files
[docs-fs-sharing]: https://docs.hopsworks.ai/5.2/user_guides/fs/sharing/sharing/
[docs-serving-logs]: https://docs.hopsworks.ai/5.2/user_guides/mlops/serving/troubleshooting/#configuring-disk-logging
[docs-feature-logging]: https://docs.hopsworks.ai/5.2/user_guides/fs/feature_view/deployment/#feature-logging
[docs-trino-sharing]: https://docs.hopsworks.ai/5.2/user_guides/projects/trino/sharing/
[docs-trino-private]: https://docs.hopsworks.ai/5.2/user_guides/projects/trino/catalogs/#private-catalogs
[docs-superset-backup]: https://docs.hopsworks.ai/5.2/setup_installation/admin/ha-dr/dr/#superset-backup
[docs-teleport]: https://docs.hopsworks.ai/5.2/user_guides/projects/terminal/teleport/
[docs-kafka4-expect]: https://docs.hopsworks.ai/5.2/setup_installation/admin/kafka4/#what-to-expect
[docs-kafka4-clients]: https://docs.hopsworks.ai/5.2/setup_installation/admin/kafka4/#kafka4-client-compatibility
[docs-os-faiss]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade_opensearch/#embedding-indices-move-to-faiss
[values-hopsfs-csi]: https://docs.hopsworks.ai/5.2/setup_installation/common/helm_chart_values/hopsfs-csi/
[values-hw-keda]: https://docs.hopsworks.ai/5.2/setup_installation/common/helm_chart_values/hw-keda/
[values-kafka]: https://docs.hopsworks.ai/5.2/setup_installation/common/helm_chart_values/kafka/#helm.kafka.cluster.kafka.config.minInsyncReplicas
[values-hopsworks-analytics]: https://docs.hopsworks.ai/5.2/setup_installation/common/helm_chart_values/hopsworks/#helm.hopsworks.variables.hopsworks_analytics
[values-netpol]: https://docs.hopsworks.ai/5.2/setup_installation/common/helm_chart_values/hopsworks/#helm.hopsworks.variables.project_namespace_network_policy_enabled
[docs-os]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade_opensearch/
[docs-upgrade-before]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/#upgrade-before-every-upgrade
[docs-upgrade-51-to-52]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/#upgrade-51-to-52
[docs-upgrade-changes-from-522]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/#upgrade-changes-from-522
[docs-upgrade-onlinefs]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/#upgrade-onlinefs-topics
[docs-upgrade-ddl]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/#upgrade-ddl-sql-node
[docs-upgrade-otlp]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/#upgrade-agent-otlp-sidecar
[docs-upgrade-kueue-external]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/#upgrade-kueue-external
[docs-upgrade]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade/
[docs-helm4]: https://docs.hopsworks.ai/5.2/setup_installation/admin/helm4/
[docs-kueue-what-changes]: https://docs.hopsworks.ai/5.2/setup_installation/admin/kueue_external/#what-changes
[docs-run-ai]: https://docs.hopsworks.ai/5.2/setup_installation/admin/run_ai/
[docs-keda-crds]: https://docs.hopsworks.ai/5.2/setup_installation/admin/keda/#keda-crds
[docs-os-restore-crash-loop]: https://docs.hopsworks.ai/5.2/setup_installation/admin/ha-dr/dr/#opensearch-restore-crash-loop
[docs-os-security]: https://docs.hopsworks.ai/5.2/setup_installation/admin/upgrade_opensearch/#opensearch-security-configuration
[docs-node-pools]: https://docs.hopsworks.ai/5.2/setup_installation/admin/node_pools/
[docs-buildkitd-rhel8]: https://docs.hopsworks.ai/5.2/setup_installation/admin/build_performance/#buildkitd-rhel8
[docs-sharepoint]: https://docs.hopsworks.ai/5.2/user_guides/fs/data_source/creation/sharepoint/
[docs-sharepoint-client]: https://docs.hopsworks.ai/5.2/user_guides/fs/data_source/creation/sharepoint/#client-version
[docs-ds-hopsworks]: https://docs.hopsworks.ai/5.2/user_guides/fs/data_source/creation/hopsworks/
[docs-ds-sqlserver]: https://docs.hopsworks.ai/5.2/user_guides/fs/data_source/creation/sql/#sql-server-specific-notes
[docs-apps-public]: https://docs.hopsworks.ai/5.2/user_guides/projects/apps/#public-access-for-streamlit-apps
[docs-setup-aws]: https://docs.hopsworks.ai/5.2/setup_installation/aws/getting_started/
[docs-setup-azure]: https://docs.hopsworks.ai/5.2/setup_installation/azure/getting_started/

### Python client

<div class="hops-client-notes" data-versions="5.2.0 5.2.1" markdown>

The release notes of hopsworks-api [5.2.0](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.2.0), [5.2.1](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.2.1).

</div>

