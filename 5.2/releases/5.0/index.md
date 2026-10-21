# Release notes { #hopsworks-5-0 }

The releases of Hopsworks 5.0, newest first.

## 5.0.14 { #hopsworks-5-0-14 }

Helm chart 5.0.14 · Released on 2026-10-16 · Changes since [5.0.13][hopsworks-5-0-13] · Python client [5.0.10](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.10)

### What's changed

- <span class="hops-release-tag">API</span> Changed Spark engine reads of a feature view whose feature groups all come from one Snowflake data source to run the whole query, point-in-time joins included, in Snowflake instead of reading each feature group in full.
- <span class="hops-release-tag">API</span> Fixed external Spark clients failing to read, write or delete DELTA feature groups when the HopsFS NameNode and DataNode load balancers are disabled.
- <span class="hops-release-tag">API</span> Fixed the Python client installing pyarrow 26 next to NumPy 1, with which importing `hsfs.feature_group` fails with `pyarrow requires NumPy 2.0 or newer`; it now requires pyarrow below 26.
- <span class="hops-release-tag">API</span> Fixed `SnowflakeConnector.connector_options()` in the Python client failing for a data source with key pair authentication, and leaving out the role set on the data source.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Addressed vulnerabilities in jobs.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added key pair authentication to DLT ingestion from Snowflake, which accounts that enforce MFA need ([Snowflake data source](https://docs.hopsworks.ai/5.0/user_guides/fs/data_source/creation/snowflake/)).
- <span class="hops-release-tag">API</span> Fixed reads of external feature groups failing on chained point-in-time joins, on joins with a prefix or extra primary keys, and after features were appended to the feature group.
- <span class="hops-release-tag">API</span> Fixed Snowflake reads through the Hopsworks Query Service, such as Python engine reads, using the Snowflake user's default role instead of the role set on the data source.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed queued project and member operations, such as Superset access changes, failing for good after 68 attempts, for project Observers, or when removing a member's Superset user; a failing operation now retries once a day.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed a killed Spark job with pending executors staying in the aggregating logs state for up to 20 minutes.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed Trino giving a project access to only one of several feature stores shared with it.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed Git operations failing with an I/O error when a Git provider proxy is set on a cluster with HopsFS encryption at rest.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed DLT ingestion rejecting a load whose decimal values are narrower than the precision of the source column.
- <span class="hops-release-tag">FRONTEND</span> Added a **Stop** button to the SQL Runner, and leaving the SQL Runner now stops the running query; the run status shows the elapsed time and the rows the query engine processed.
- <span class="hops-release-tag">FRONTEND</span> Changed the SQL Runner layout: the results fill the panel with row numbers and column types, the Explorer lists a table's columns, autocomplete completes the statement at the cursor, `EXPLAIN` plans render as JSON or a graph, and long plans and table definitions scroll instead of overflowing.
- <span class="hops-release-tag">FRONTEND</span> Fixed dialogs, drawers and menus opened from a table row closing by themselves on the Git, model deployment, alert receiver and feature monitoring pages, and the Git Status drawer never opening.
- <span class="hops-release-tag">HELM</span> Fixed RonDB daemons ignoring SIGTERM, so every pod stop ended in a kill and a stopped data node could stall the cluster.
- <span class="hops-release-tag">HELM</span> Changed the RonDB REST server probes to use their own port, which keeps answering while a stalled data node blocks requests.
- <span class="hops-release-tag">HELM</span> Fixed the RonDB REST server reading request bodies over 64 KiB as empty.
- Addressed vulnerabilities in the Payara, Hopsworks, Hopsworks CA, Hive Metastore, remote shuffle service, Spark operator, Filebeat, Logstash, Grafana, OpenSearch Dashboards and Superset images, in the MySQL and Redis of Superset, and in the Hopsworks server dependencies.
- Fixed HopsFS staying in safe mode after all NameNodes restarted, a NameNode restart occasionally marking every block as corrupt, and NameNodes running out of file descriptors when many blocks are corrupt.

### Breaking changes

- <span class="hops-release-tag">HELM</span> The chart no longer turns on Trino's MySQL event listener, which crash-looped the Trino coordinator after a MySQL server restart, so the [Trino query history](https://docs.hopsworks.ai/5.0/setup_installation/admin/trino/#query-history) stops recording queries.
  To keep it, add the listener to your values as the chart's `README.md` shows; recorded queries now lose their text and plan after `hopsworks.variables.trino_events_strip_details_after_days`, from the first nightly cleanup, so set it to `61` or more to keep them.
- <span class="hops-release-tag">HELM</span> The RonDB REST server now uses a probe port and an upload path that need RonDB 26.02.11, instead of probing its REST port with no upload path.
  If you pin `rondb.rondb.images.rondb.tag` to an older version, set `rondb.rondb.rdrs.probePort.enabled: false` and `rondb.rondb.rdrs.uploadPath: ""`, or the REST server doesn't start.

### Upgraded components

| Component | 5.0.13 | 5.0.14 |
| --------- | ------ | ------ |
| Base environment images | 5.0.10 | 5.0.11 |
| Filebeat | 8.19.20 | 8.19.23 |
| Git image | 1.4.4 | 1.4.5 |
| Grafana | 10.2.2-h7 | 10.2.2-h7.1 |
| Hive Metastore | 3.0.0.14.7-v1 | 3.0.0.14.7-v2 |
| HopsFS | 3.4.3.1-EE-RC5 | 3.4.3.1-EE-RC6 |
| Logstash | 7.17.9.5 | 7.17.9.6 |
| OpenSearch Dashboards | 1.3.6.7 | 1.3.6.8 |
| Payara | 6.2024.12-jdk17.8 | 6.2024.12-jdk17.8.1 |
| Remote shuffle service (Apache Uniffle) | 0.10.8.1 | 0.10.8.2 |
| RonDB | 26.02.10 | 26.02.11 |
| Spark operator | 2.2.1-h4 | 2.2.1-h6 |
| Superset | 6.0.0p3.4 | 6.0.0p3.5 |
| Superset init image | dockerize | 0.14.0-alpine-h1.1 |
| Superset MySQL | 8.4.5-debian-12-r0-h3 (MySQL 8.4.11) | 8.4.11-ubuntu24.04-h5 |
| Superset MySQL metrics exporter | 0.17.2-debian-12-r7 | 0.20.0-alpine-h1.1 |
| Superset MySQL volume permissions image | 12-debian-12-r43 | 12-alpine-h1.1 |
| Superset Redis | 7.4.9-bookworm | 7.4.11-alpine-h2 |
| Superset Redis metrics exporter | 1.50.0-debian-11-r1 | 1.90.0-alpine-h1.1 |
| Trino | 480.1-v1 | 480.1-v2 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed `rondb.rondb.terminationGracePeriodSeconds` to one value per component, 300 seconds for `ndbmtds` instead of 60, and 30 for the others; a plain number still sets the data nodes only.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.variables.trino_events_strip_details_after_days` (default `7`) and changed the default `hopsworks.variables.trino_events_cleaner_batch_size` to `100`.
- <span class="hops-release-tag">HELM</span> Changed the Superset init image to its own `superset/dockerize` repository, also in `values.airgap.yaml`; if you override `superset.superset.initImage.repository`, point it at a mirror of that image.
- <span class="hops-release-tag">HELM</span> Fixed `values.openshift.yaml` for the OpenShift `restricted-v2` SCC, covering Strimzi, Kafka, ZooKeeper, the Spark operator, Trino, Prometheus and the MySQL and Redis of Superset; it also turns off the Prometheus node exporter, which OpenShift ships itself.
  Use it with Helm 4 or Helm 3.17.1 or later, because older Helm 3 releases can ignore its `null` overrides of subchart values.
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.rdrs.probePort`, the port of the RonDB REST server probes, `rondb.rondb.rdrs.uploadPath`, where it buffers request bodies over 64 KiB, and `rondb.rondb.rdrs.maxKeepaliveRequests` to rebalance client connections after a rolling restart.
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.rdrs.ttlPurge.enable` and `activeWindow` to turn off the TTL purge of the RonDB REST server or limit it to a daily window.

### Python client

<div class="hops-client-notes" data-versions="5.0.10" markdown>

The release notes of hopsworks-api [5.0.10](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.10).

</div>

## 5.0.13 { #hopsworks-5-0-13 }

Helm chart 5.0.13 · Released on 2026-09-28 · Changes since [5.0.12][hopsworks-5-0-12] · Python client [5.0.9](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.9), unchanged

### What's changed

- <span class="hops-release-tag">API</span> Fixed offline reads of a feature view or query that includes a spine group failing with an HTTP 500 error.
- <span class="hops-release-tag">HELM</span> Fixed the Airflow migration Job losing tables on clusters with more than one RonDB MySQL server, so the upgrade never completed; it now uses only the DDL MySQL server.
  A cluster whose earlier upgrade already failed this way needs the Airflow schema dropped on every MySQL server before it upgrades again.
- <span class="hops-release-tag">HELM</span> Fixed OnlineFS upgrades stalling on a full node pool: with two or more `onlinefs.deployment.replicas`, a rollout now replaces one pod at a time.
- <span class="hops-release-tag">HELM</span> Changed the RonDB REST server probes to outlast a data node failure.
- <span class="hops-release-tag">HELM</span> Fixed the OpenSearch metrics exporter reporting the parent cluster on a satellite deployment, and missing OpenSearch with a custom `global._hopsworks.consulDomainName`.

### Breaking changes

- <span class="hops-release-tag">HELM</span> The Airflow migration Job now waits for the DDL MySQL server, instead of any MySQL server, so the upgrade hangs with `rondb.rondb.meta.ddlMySQLd.enabled: false`.
  Keep it enabled if you use Airflow.
- <span class="hops-release-tag">HELM</span> Removed the Kafka quota defaults Job, which applied `kafka.cluster.kafka.quotas.defaults` to each client, so it is ignored with `type: strimzi`, and fails the render with `type: kafka` when `enabled` is `true`.
  If you set it, move your limits to `kafka.cluster.kafka.quotas.strimzi` or `kafka.cluster.kafka.quotas.kafka`, with new numbers: the `strimzi` rates are a total per broker, shared by all clients.
- <span class="hops-release-tag">HELM</span> `quotas` on a user in `kafka.users` now fail the render under the default `strimzi` plugin, which never enforced them.
  Remove them, or set `kafka.cluster.kafka.quotas.type: kafka`, `kafka.cluster.kafka.quotas.minAvailableRatioPerVolume: 0` and `kafka.cluster.kafka.authorizer.principalBuilderClass: ""` to enforce them.

### Deprecations

- <span class="hops-release-tag">HELM</span> Deprecated `kafka.cluster.kafka.quotas.defaults`, which is no longer honoured; see the breaking changes above.

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `kafka.cluster.kafka.quotas.type` to choose the Kafka quota plugin: `strimzi` (the default), set under `kafka.cluster.kafka.quotas.strimzi`, or `kafka`, which enforces per-user quotas from `kafka.users[].quotas` and defaults from `kafka.cluster.kafka.quotas.kafka`.
  `kafka` also needs `kafka.cluster.kafka.quotas.minAvailableRatioPerVolume: 0`, per-user quotas need `kafka.cluster.kafka.authorizer.principalBuilderClass: ""`, which changes how ACLs and `superUsers` match principals, and none of these settings take effect on clusters first installed on Hopsworks 4.3 or 4.5.
- <span class="hops-release-tag">HELM</span> Added `onlinefs.deployment.strategy` to set the rollout strategy of OnlineFS.
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.rdrs.probes` to configure the RonDB REST server probes.

### Python client

The Python client is unchanged, 5.0.9.

## 5.0.12 { #hopsworks-5-0-12 }

Helm chart 5.0.12 · Released on 2026-09-21 · Changes since [5.0.11][hopsworks-5-0-11] · Python client 5.0.9 ([5.0.8](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.8), [5.0.9](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.9))

### What's changed

- <span class="hops-release-tag">API</span> Fixed the Python client installing PyMySQL 1.2.1 or later, with which `import hsfs` fails with an `ImportError`; it now requires PyMySQL below 1.2.1.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added **Driver overhead factor** and **Executor overhead factor** to the Spark configuration of jobs, Jupyter and project defaults, which set `spark.driver.memoryOverheadFactor` and `spark.executor.memoryOverheadFactor`, also as `driver_memory_overhead_factor` and `executor_memory_overhead_factor` of `JobConfiguration` in the Python client ([PySpark jobs](https://docs.hopsworks.ai/5.0/user_guides/projects/jobs/pyspark_job/)).
  Jupyter Spark sessions now default both to `0.4`, as PySpark jobs do, so notebooks and their executors request more memory than before.
- <span class="hops-release-tag">FRONTEND</span> Added a **Monitor** button to each running Spark application on the Jupyter page, showing its driver and executor pods, and fixed the Jupyter server monitor showing a Spark executor as the notebook pod; the Grafana link there is now called **Metrics**.
- <span class="hops-release-tag">HELM</span> Changed the memory split of the remote shuffle servers to stop heap out-of-memory crashes: 20% of `spark.rss.shuffleServer.resources.limits.memory` is reserved for native memory and the rest is split 25% heap, 75% off-heap.
- <span class="hops-release-tag">HELM</span> Fixed the remote shuffle servers never receiving their off-heap memory setting, which left their buffers about nine times too small.
- <span class="hops-release-tag">HELM</span> Fixed service logs being dropped from the `.services` OpenSearch index when Kubernetes labels pushed it past 1000 fields.

### Breaking changes

- <span class="hops-release-tag">HELM</span> Removed `hopsworks.variables.jupyter_spark_driver_memory_overhead_factor`, added in 5.0.10; the driver memory overhead of a notebook now comes from its Spark configuration.
  Remove it from your values before you upgrade, because the values schema rejects it; with `--reuse-values`, which keeps the old default, use `--reset-then-reuse-values`.
- <span class="hops-release-tag">HELM</span> Filebeat now keeps only the Kubernetes labels listed under `olk.filebeat.kubernetesMetadata`, and the `serviceLabelKey` of each `olk.logstash.extendServicesPipeline` entry, on service logs, instead of every pod label.
  Add any other label you filter on to `olk.filebeat.kubernetesMetadata.extraPodLabels`, `extraNamespaceLabels` or `extraNodeLabels`; an overridden `olk.filebeat.logs_locations` keeps its own `add_kubernetes_metadata` processor and every label, so to get the fix set `addKubernetesMetadata: true` on each entry and drop that processor.

### Upgraded components

| Component | 5.0.11 | 5.0.12 |
| --------- | ------ | ------ |
| Base environment images | 5.0.9 | 5.0.10 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed the default `spark.rss.shuffleServer.bufferCapacityRatio` to `0.6` and `spark.rss.shuffleServer.resources.limits.cpu` to `2000m`.

### Python client

<div class="hops-client-notes" data-versions="5.0.8 5.0.9" markdown>

The release notes of hopsworks-api [5.0.8](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.8), [5.0.9](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.9).

</div>

## 5.0.11 { #hopsworks-5-0-11 }

Helm chart 5.0.11 · Released on 2026-09-16 · Changes since [5.0.10][hopsworks-5-0-10] · Python client [5.0.7](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.7), unchanged

### What's changed

- <span class="hops-release-tag">HELM</span> Fixed RonDB data nodes reporting ready while the management server is unreachable, which let a rollout stop the healthy data node of the same node group and shut the cluster down; their liveness probe also no longer kills them then.
- <span class="hops-release-tag">HELM</span> Fixed RonDB rolling restarts making healthy data nodes shut down; data nodes now wait for the rollout to settle (`rondb.rondb.ndbmtdSettleWait`).
- <span class="hops-release-tag">HELM</span> Fixed the RonDB MySQL setup and in-place restore Jobs failing for good when data nodes took more than about two hours to start, and the MySQL servers crash-looping while they wait for a backup restore.
- <span class="hops-release-tag">HELM</span> Changed the Kafka Entity Operator, when `kafka.cluster.entityOperator` is set, to follow the tolerations, topology spread and node affinity of the Kafka brokers, unless set under `kafka.cluster.entityOperator.template.pod`.

### Upgraded components

| Component | 5.0.10 | 5.0.11 |
| --------- | ------ | ------ |
| RonDB | 26.02.9 | 26.02.10 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed `rondb.rondb.timeoutsMinutes.ndbmtdStartupProbe` to 240 minutes, from 120, so data nodes that take over two hours to reload aren't restarted in a loop.
- <span class="hops-release-tag">HELM</span> Fixed users added to `rondb.rondb.mysql.users` after the install never being created, and users with privileges on a single database or table failing; every upgrade now creates the listed users.
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.mysql.users[].existingSecret` to read the password of a MySQL user from your own Secret.

### Python client

The Python client is unchanged, 5.0.7.

## 5.0.10 { #hopsworks-5-0-10 }

Helm chart 5.0.10 · Released on 2026-09-11 · Changes since [5.0.9][hopsworks-5-0-9] · Python client [5.0.7](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.7)

### What's changed

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added the GitHub Copilot and OpenCode CLIs to the web terminal, next to Claude Code and Codex; the coding agents share the skills in your home directory, which `hops skills list` now lists, and keep their logins and sessions across terminal sessions.
- <span class="hops-release-tag">API</span> Fixed creating an agent task with `JobApi.create_job` in the Python client failing with `'appPath' not set in job configuration`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Addressed vulnerabilities in project datasets.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed Git operations failing to authenticate when a provider token or password contains special characters.
  The upgrade converts the saved credentials; if a provider still fails, save it again in your account settings.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed `GET /project/{id}/jupyter/running` to return `204 No Content` instead of `404` when no Jupyter server is running.
- <span class="hops-release-tag">API</span> Fixed writes to an online feature group failing after its table was truncated.
- <span class="hops-release-tag">FRONTEND</span> Added GitHub Copilot as a provider of agent tasks, signed in with the token of a GitHub AI provider or of your github.com Git provider, which can't be a classic personal access token.
- <span class="hops-release-tag">FRONTEND</span> Added a Copilot tab and a Business Glossary for each coding agent to the Agents page of the cluster settings, now called Coding Agents.
- <span class="hops-release-tag">FRONTEND</span> Fixed Spark executors started from Jupyter having no CPU limit and rounding fractional executor cores up to a whole core; they now get the executor cores of the session as request and limit, as job executors do.
- <span class="hops-release-tag">FRONTEND</span> Added the status of the TTL purge of online feature groups: a TTL purge card on the feature group overview and a TTL Purge page in the cluster settings.
- <span class="hops-release-tag">FRONTEND</span> Added a read-only `featurestore` directory to the web terminal with the files of the project's offline feature store.
- <span class="hops-release-tag">FRONTEND</span> Fixed creating or editing a BigQuery data source failing with a gRPC error when the form lists the GCP projects.
- <span class="hops-release-tag">FRONTEND</span> Fixed uploading files in the file browser deleting the files of the previous upload unless the page was reloaded.
- <span class="hops-release-tag">FRONTEND</span> Fixed the project home page crashing with "Something went wrong while loading this page" because of the Feature usage card.
- <span class="hops-release-tag">FRONTEND</span> Removed the Plans dialog from the web terminal.
- <span class="hops-release-tag">FRONTEND</span> Fixed `kubectl` failing in every web terminal, because terminals never got a kubeconfig.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Fixed the Jupyter server of Spark environments running out of memory: it now adds an overhead (`hopsworks.variables.jupyter_spark_driver_memory_overhead_factor`) and a floor (`jupyter_spark_notebook_server_memory_floor_mb`) to the driver memory.
- <span class="hops-release-tag">HELM</span> Added support for Git repositories behind a private CA or an HTTP proxy: set `hopsworks.variables.git_custom_ca_configmap` to a ConfigMap that exists in every project namespace, `git_<provider>_http_proxy` and `git_<provider>_https_proxy` for GitHub, GitLab and BitBucket, or `git_disable_tls_verification`.
- <span class="hops-release-tag">HELM</span> Fixed init containers that wait for a RonDB rollout giving up after 30 minutes, which failed the Hive Metastore, HopsFS, Airflow migration and CA pods when a RonDB restart took longer.
- <span class="hops-release-tag">HELM</span> Added the `KafkaAuthenticationFailures` Prometheus alert for clients that keep failing to authenticate, for example with an expired certificate.
- Addressed vulnerabilities in the ArrowFlight and remote shuffle service images.

### Breaking changes

- <span class="hops-release-tag">FRONTEND</span> Removed the Code Style, Testing and Security rules from the Coding Agents page of the cluster settings, and web terminals no longer give them to the coding agents.
  If you set them, move what you need to the new Business Glossary or the system-wide instructions of each agent.

### Upgraded components

| Component | 5.0.9 | 5.0.10 |
| --------- | ----- | ------ |
| ArrowFlight server | 5.0.3 | 5.0.4 |
| Base environment images | 5.0.8 | 5.0.9 |
| Git image | 1.4.2 | 1.4.4 |
| OnlineFS | 2.3.4 | 2.3.5 |
| Remote shuffle service (Apache Uniffle) | 0.10.8 | 0.10.8.1 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `kafka.cluster.clusterCa.validityDays` and `kafka.cluster.clientsCa.validityDays` (default `365`) for the certificates Strimzi issues, applied at the next issuance.

### Python client

<div class="hops-client-notes" data-versions="5.0.7" markdown>

The release notes of hopsworks-api [5.0.7](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.7).

</div>

## 5.0.9 { #hopsworks-5-0-9 }

Helm chart 5.0.9 · Released on 2026-08-28 · Changes since [5.0.8][hopsworks-5-0-8] · Python client [5.0.6](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.6)

### What's changed

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed feature statistics failing to save when a column has a NaN or infinite statistic, such as the standard deviation of an all-null column, statistics of DELTA feature groups being computed on the previous commit, and wrong uniqueness statistics, which are now left empty when `exact_uniqueness` is off.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed Ray jobs and the Ray clusters of Jupyter failing to start on OpenShift, because their pods couldn't read the project certificates.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed the state of a Spark or Ray job execution switching between queued and running while one of its pods couldn't be scheduled.
- <span class="hops-release-tag">FRONTEND</span> Fixed web terminals failing to start on OpenShift.
- <span class="hops-release-tag">HELM</span> Fixed init containers without requests and limits being charged a namespace `LimitRange` default for the whole life of the pod; the RonDB and Consul charts are not covered yet.
- <span class="hops-release-tag">HELM</span> Fixed the KServe local model controller image (`kserve.kserve.localmodel.enabled`) failing to pull.
- Addressed vulnerabilities in the Hopsworks and Hopsworks CA images.

### Breaking changes

- <span class="hops-release-tag">HELM</span> Init containers now set CPU and memory requests and limits, which most of them used to leave unset.
  If a namespace `LimitRange` has a `min` or `max` they don't fit, set `global._hopsworks.initContainerResources` before you upgrade, or Kubernetes rejects the pods.

### Upgraded components

| Component | 5.0.8 | 5.0.9 |
| --------- | ----- | ----- |
| ArrowFlight server | 5.0.2 | 5.0.3 |
| Base environment images | 5.0.5 | 5.0.8 |
| Git image | 1.4.1 | 1.4.2 |
| Hive Metastore | 3.0.0.14.6-v1 | 3.0.0.14.7-v1 |
| HopsFS | 3.4.3.1-EE-RC4 | 3.4.3.1-EE-RC5 |
| Model serving webhook | 5.0.2 | 5.0.3 |
| RonDB | 26.02.8 | 26.02.9 |
| Spark history server | 3.5.5.2 | 3.5.5.3 |
| Toolbox image (hwutils) | 1.7.1 | 1.7.2 |
| Trino | 480-v6 | 480.1-v1 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed `hopsworks.variables.docker_operations_timeout_listing_minutes`, `docker_operations_timeout_check_minutes` and `docker_operations_timeout_export_minutes` from 5 to 15 minutes, and `max_concurrent_base_sync_ops` from 6 to 5, so large base images finish syncing.
- <span class="hops-release-tag">HELM</span> Fixed the chart failing to render with `hopsworks.dropDatabase: true`.
- <span class="hops-release-tag">HELM</span> Added `global._hopsworks.initContainerResources` (`waiter`, `tool`, `runtime`) to size the requests and limits that init containers now set.

### Python client

<div class="hops-client-notes" data-versions="5.0.6" markdown>

The release notes of hopsworks-api [5.0.6](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.6).

</div>

## 5.0.8 { #hopsworks-5-0-8 }

Helm chart 5.0.8 · Released on 2026-08-21 · Changes since [5.0.6][hopsworks-5-0-6] · Python client 5.0.5 ([5.0.4](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.4), [5.0.5](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.5))

### What's changed

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added an upload policy, `hopsworks.variables.upload_policy`, that allows file uploads to everyone (`enabled`), to administrators only (`admins_only`) or to nobody (`disabled`), from the web UI and from clients such as the Python SDK; the web UI disables its upload controls for blocked users, and `upload(overwrite=True)` in the Python client no longer deletes the existing file when the policy blocks the upload.
  The upgrade writes the value to the cluster, so a change made in the admin UI lasts until the next upgrade.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added deleting rows from a feature group with `remove_rows` in the Python client and `removeRows` in the Java client, which delete from both stores of an online feature group unless `storage` is `"offline"` or `"online"`; online ingestion results and the ingestion activity in the UI show them as deleted rows.
- <span class="hops-release-tag">API</span> Added project member management (`Project.get_members`, `add_member`, `remove_member`), feature group access for members with the Feature store restricted role (`FeatureGroup.grant_restricted_access`, `revoke_restricted_access`, `get_restricted_access`) and user administration (`hopsworks.get_users_api()`) to the Python client.
- <span class="hops-release-tag">API</span> Fixed `share`, `shared_with` and `unshare` of feature groups and feature stores, and `DatasetApi.unshare`, failing in the Python client.
- <span class="hops-release-tag">API</span> Fixed online feature vectors failing with a `ValueError` on `TIMESTAMP(n)` and `DATETIME(n)` features with fractional seconds, and similarity search results losing the milliseconds of timestamps.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Addressed vulnerabilities in feature views.
- <span class="hops-release-tag">API</span> Fixed Arrow Flight queries of partitioned DELTA feature groups failing once the feature group is older than `delta.logRetentionDuration`.
- <span class="hops-release-tag">FRONTEND</span> Fixed the **SQL Runner** returning a different number of rows on each run, ignoring the row limit for some queries and discarding the typed query when the limit changed; it now shows the row count of a result.
- <span class="hops-release-tag">FRONTEND</span> Fixed project members with the Observer role being offered Superset actions that fail; the UI now disables them.
- <span class="hops-release-tag">HELM</span> Fixed Superset queries through Trino logging `Access Denied: Cannot access catalog system`, and project creation and new project members logging Trino `Access Denied: Invalid credentials` errors before their Superset connection is ready.
- <span class="hops-release-tag">HELM</span> Fixed Kafka failing to start on clusters with many API groups, such as OpenShift, when the Vertical Pod Autoscaler (`global._hopsworks.vpaEnabled`) is on, because pod creation stalled for about 20 seconds.
- Addressed vulnerabilities in the Hopsworks server dependencies, in the Payara, HopsFS, Hive Metastore, ArrowFlight, OnlineFS, Airflow, Git, toolbox, base environment, Filebeat, Grafana, OpenSearch, OpenSearch Dashboards, Logstash, remote shuffle service and Superset images, and in the MySQL database of Superset.

### Breaking changes

- <span class="hops-release-tag">HELM</span> `vpa.image.tag` is now a string, instead of a number, so an override such as `1.2` fails the values schema.
  If you override it, quote it and set it equal to `vpa.imagesTag` before you upgrade; with `--reuse-values` and `global._hopsworks.vpaEnabled: true`, use `--reset-then-reuse-values`.

### Deprecations

- <span class="hops-release-tag">API</span> Deprecated `FeatureGroup.commit_delete_record` in the Python client for `remove_rows`, which also deletes the rows from the online store.

### Upgraded components

| Component | 5.0.6 | 5.0.8 |
| --------- | ----- | ----- |
| Airflow image | 5.0.1 | 5.0.2 |
| ArrowFlight server | 5.0.0 | 5.0.2 |
| Base environment images | 5.0.2 | 5.0.5 |
| Filebeat | 8.15.0 | 8.19.20 |
| Git image | 1.4 | 1.4.1 |
| Grafana | 10.2.2-h6 | 10.2.2-h7 |
| Hive Metastore | 3.0.0.14.5-v1 | 3.0.0.14.6-v1 |
| HopsFS | 3.4.3.1-EE-RC3 | 3.4.3.1-EE-RC4 |
| Logstash | 7.17.9.4 | 7.17.9.5 |
| OnlineFS | 2.3.2 | 2.3.4 |
| OpenSearch | 1.3.6.2 | 1.3.6.3 |
| OpenSearch Dashboards | 1.3.6.6 | 1.3.6.7 |
| Payara | 6.2024.12-jdk17.6 | 6.2024.12-jdk17.8 |
| Remote shuffle service (Apache Uniffle) | 0.10.7 | 0.10.8 |
| Spark | 3.5.5.1 | 3.5.5.2 |
| Spark history server | 3.5.5.1 | 3.5.5.2 |
| Superset | 6.0.0p2.4 | 6.0.0p3.4 |
| Superset MySQL | 8.4.5-debian-12-r0-h2 (MySQL 8.4.10) | 8.4.5-debian-12-r0-h3 (MySQL 8.4.11) |
| Toolbox image (hwutils) | 1.7 | 1.7.1 |
| Vertical Pod Autoscaler | 1.2.1 | 1.7.1 |
| Vertical Pod Autoscaler installer | 1.2 | 1.7.1 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `hopsworks.jupyter.notebookConfig.enableUploads`; set it to `false` to block file uploads from the Jupyter UI and base64 uploads through the Jupyter Contents API.

### Python client

<div class="hops-client-notes" data-versions="5.0.4 5.0.5" markdown>

The release notes of hopsworks-api [5.0.4](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.4), [5.0.5](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.5).

</div>

## 5.0.6 { #hopsworks-5-0-6 }

Helm chart 5.0.6 · Released on 2026-07-29 · Changes since [5.0.5][hopsworks-5-0-5] · Python client [5.0.3](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.3), unchanged

### What's changed

- <span class="hops-release-tag">FRONTEND</span> Added the full color palette, interface styles such as border radius, border width and shadows, a live preview, and a branding export, import and reset to the Branding page of the cluster settings, shown when the cluster variable `enable_custom_branding` is `true`.
- <span class="hops-release-tag">FRONTEND</span> Fixed the create and edit forms of model and LLM deployments: a hidden tracing field could block saving, the vLLM image tag could stay blank, and editing a deployment reset its resources.
- <span class="hops-release-tag">FRONTEND</span> Fixed feature monitoring charts not showing the standard deviation.
- Addressed vulnerabilities in the Hopsworks and Hopsworks CA images.
- Fixed Airflow failing to start on OpenShift with `airflow: command not found`.

### Upgraded components

| Component | 5.0.5 | 5.0.6 |
| --------- | ----- | ----- |
| Airflow image | 5.0.0 | 5.0.1 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `docker-registry.affinity`, `minio.affinity` and `hopsworks.dockerRegistry.preset.affinity` for the Docker registry, MinIO and the preset image push Job.

### Python client

The Python client is unchanged, 5.0.3.

## 5.0.5 { #hopsworks-5-0-5 }

Helm chart 5.0.5 · Released on 2026-07-22 · Changes since [5.0.4][hopsworks-5-0-4] · Python client [5.0.3](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.3), unchanged

### Upgraded components

| Component | 5.0.4 | 5.0.5 |
| --------- | ----- | ----- |
| RonDB | 26.02.6 | 26.02.8 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.timeoutsMinutes.ndbmtdStartupProbe` and `rondb.rondb.timeoutsMinutes.mysqldStartupProbe`, the startup probe timeouts of the RonDB data nodes and MySQL servers, and raised them from 25 to 120 minutes and from about 17 to 20 minutes.
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.ndbmtdSequencedRollout.enabled` (default `false`) to roll the RonDB data nodes one node group at a time, on clusters with more than one node group.

### Python client

The Python client is unchanged, 5.0.3.

## 5.0.4 { #hopsworks-5-0-4 }

Helm chart 5.0.4 · Released on 2026-07-17 · Changes since [5.0.3][hopsworks-5-0-3] · Python client [5.0.3](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.3), unchanged

### What's changed

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">HELM</span> Fixed online feature store ingestion skipping rows that failed with transient RonDB errors, such as `Rowid already allocated`, during a RonDB upgrade; it now pauses and retries until they are written.
  Set the pause between retries with `onlinefs.configmapOverrides.onlinefssite.service.pauseRetrySleepMs` (default `5000`).

### Upgraded components

| Component | 5.0.3 | 5.0.4 |
| --------- | ----- | ----- |
| OnlineFS | 2.3.1 | 2.3.2 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `hopsworks.service.worker.external.https.nodePort` to pin the node port of the Hopsworks HTTPS service when its `type` is `NodePort`.

### Python client

The Python client is unchanged, 5.0.3.

## 5.0.3 { #hopsworks-5-0-3 }

Helm chart 5.0.3 · Released on 2026-07-14 · Changes since [5.0.2][hopsworks-5-0-2] · Python client [5.0.3](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.3), unchanged

### What's changed

- <span class="hops-release-tag">HELM</span> Fixed the Hopsworks admin pod getting stuck in CrashLoopBackOff when workers were slow to register; the admin sidecar now keeps retrying, restarts unregistered workers and redeploys Hopsworks after the admin container restarts.
- <span class="hops-release-tag">HELM</span> Changed the Hopsworks admin container to restart after about 10 minutes without a response (`hopsworks.probs.admin.livenessProbe`), with up to 30 minutes to start (`hopsworks.probs.admin.startupProbe`).
- Addressed vulnerabilities in the Strimzi operator and Kafka images.

### Breaking changes

- <span class="hops-release-tag">HELM</span> The admin liveness and startup probes now have a default `exec` handler, instead of no probe, so a probe you set with `httpGet` or `tcpSocket` also needs `exec: null`, or Kubernetes rejects the pod.
  This needs Helm 4 or Helm 3.17.1 or later, because older Helm 3 releases ignore a `null` for a subchart value.

### Deprecations

- <span class="hops-release-tag">HELM</span> Deprecated `hopsworks.envs.postInstall.DEPLOY_RETRIES`, which has no effect now that the admin sidecar retries until the deployment succeeds.

### Upgraded components

| Component | 5.0.2 | 5.0.3 |
| --------- | ----- | ----- |
| HopsFS | 3.4.3.1-EE-RC1 | 3.4.3.1-EE-RC3 |
| RonDB | 26.02.5 | 26.02.6 |
| Strimzi | 0.45.1-h4 | 0.45.1-h5 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `hopsfs.objectStorage.s3.disableCertChecking` and `hopsfs.objectStorage.s3.disableChecksumValidation` to turn off TLS certificate checks and client-side checksum validation for the HopsFS S3 endpoint.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.envs.postInstall.DEPLOY_RETRY_INTERVAL_SEC`, `ASADMIN_TIMEOUT_SEC` and `ASADMIN_DEPLOY_TIMEOUT_SEC` to tune how the admin sidecar retries the Hopsworks deployment.

### Python client

The Python client is unchanged, 5.0.3.

## 5.0.2 { #hopsworks-5-0-2 }

Helm chart 5.0.2 · Released on 2026-07-10 · Changes since [5.0.1][hopsworks-5-0-1] · Python client 5.0.3 ([5.0.2](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.2), [5.0.3](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.3))

### What's changed

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added [mandatory tags](https://docs.hopsworks.ai/5.0/user_guides/fs/tags/mandatory_tags/) for models and deployments, and tags on deployments, which the Python client sets with `Deployment.add_tag` and reports in `missing_mandatory_tags` of a model or deployment.
  Model tags move from HopsFS to the database the first time a model is read or tagged after the upgrade, and a tag that no longer matches its schema is dropped.
- <span class="hops-release-tag">API</span> Fixed `get_tag` and `get_tags` of feature groups, feature views, training datasets, models and datasets in the Python client failing with a `TypeError` when a tag value is not a plain string.
- <span class="hops-release-tag">API</span> Fixed random splits of training datasets with the Python engine ignoring `seed`, so they were not reproducible.
- <span class="hops-release-tag">API</span> Fixed importing the Python client reseeding Python's global `random` module.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Fixed deleting a feature group failing after many online ingestions; Hopsworks now keeps the latest 1000 online ingestions of each feature group (cluster variable `online_ingestion_max_per_featuregroup`).
- <span class="hops-release-tag">API</span> Fixed online feature store ingestion stalling after a feature group with pending records was deleted.
- <span class="hops-release-tag">FRONTEND</span> Changed projects created without the `Users` dataset to get it, with a home folder for each member, automatically after the upgrade, instead of asking the data owner to enable private user directories.
- <span class="hops-release-tag">FRONTEND</span> Fixed saving the Alertmanager configuration in the admin UI silently failing when it sets a boolean or numeric option, such as `smtp_require_tls` or `max_alerts`.
- <span class="hops-release-tag">FRONTEND</span> Fixed the data preview of a feature view's logging feature group failing.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Fixed `pip install` and `uv pip install` in the web terminal, and `uv pip install` in Jupyter and Ray, ignoring the index, mirror and proxy settings in `hopsworks.dockerImage.pypi.global_parameters`.
- <span class="hops-release-tag">HELM</span> Fixed a failed HopsFS schema migration being reported as successful, which let the NameNode start on a partly migrated schema.
- <span class="hops-release-tag">HELM</span> Added post-delete Jobs that remove the Secrets, ConfigMaps and certificates `helm uninstall` used to leave behind.
- Changed LLM deployments to no longer get the inference logger sidecar, which they never used.

### Breaking changes

- <span class="hops-release-tag">HELM</span> `helm uninstall` now deletes the data volumes of the HopsFS DataNodes, MinIO, OpenSearch, Kafka, Consul and the remote shuffle service, for good, instead of keeping them.
  To keep them, set `global._hopsworks.wipeDataOnUninstall: false` or label the PVCs `hopsworks.ai/keep=true` before you uninstall; the Superset MySQL volume is deleted regardless.

### Upgraded components

| Component | 5.0.1 | 5.0.2 |
| --------- | ----- | ----- |
| Base environment images | 5.0.1 | 5.0.2 |
| Model serving webhook | 5.0.1 | 5.0.2 |
| OnlineFS | 2.3 | 2.3.1 |
| RonDB | 26.02.4 | 26.02.5 |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `cleanupOnUninstall.enabled` to each component chart, such as `hopsworks.cleanupOnUninstall.enabled`, and `certs-operator.gracefulCertTeardown.enabled`, to turn off the uninstall cleanup of that component.

### Python client

<div class="hops-client-notes" data-versions="5.0.2 5.0.3" markdown>

The release notes of hopsworks-api [5.0.2](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.2), [5.0.3](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.3).

</div>

## 5.0.1 { #hopsworks-5-0-1 }

Helm chart 5.0.1 · Released on 2026-07-03 · Changes since [5.0.0][hopsworks-5-0-0] · Python client [5.0.1](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.1)

### What's changed

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added [model monitoring](https://docs.hopsworks.ai/5.0/user_guides/mlops/model_monitoring/), which compares the inference data a deployment logs with the model's training dataset and shows the drift on the deployment page, and [distribution comparison](https://docs.hopsworks.ai/5.0/user_guides/fs/feature_monitoring/distribution_comparison/) to feature monitoring, set up in the Python client with `create_model_monitoring` of a feature view, model or deployment and `compare_on_distribution`; `hopsworks.variables.feature_monitoring_max_num_features` (default `15`) caps the features of a configuration.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added [chaining of on-demand transformation functions](https://docs.hopsworks.ai/5.0/user_guides/fs/feature_group/on_demand_transformations/#chaining-on-demand-transformations), where the output of one function feeds another, with a view of the chain on the feature group and feature view pages and from `visualize_transformations()` in the Python client, and `n_processes` of a feature group's `insert` and `save` to run independent transformations in parallel.
- <span class="hops-release-tag">API</span> Added secondary indexes for online feature groups, declared with `secondary_indexes` in `OnlineConfig` of the Python client, or `secondaryIndexes` in the Java client, when the feature group is created.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Git repositories as a source for agent deployments (`git_url`, `git_provider` and `git_branch` of `deploy_agent` in the Python client), token prices per deployment to estimate the LLM cost (`input_token_price_per_million_tokens` and `output_token_price_per_million_tokens` of `DeploymentTracingConfig`), and dashboards of traces, LLM calls, tokens, cost and tool calls.
  Agent deployments now mount HopsFS, which on clusters with Kyverno needs `hw-kyverno.policyExceptions.pythonDeployment.enabled` (default `true`).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added an app base path, a proxy routing mode and a readiness probe path to [apps](https://docs.hopsworks.ai/5.0/user_guides/projects/apps/), set in the Python client with `app_base_path` and `readiness_probe_path` of `create_app`, so an app can be served from its root without `APP_BASE_URL_PATH`; existing apps keep the prefix routing, and the App Metrics Grafana dashboard shows zero instead of "No data" for apps without traffic.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed failed or partly deleted projects counting against the owner's maximum number of projects.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed ingestion with dltHub and schema fetching from NoSQL data sources failing when the project, data source or table names are long.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added the online feature store (RonDB) quotas and memory usage to the project settings, a banner on project pages near or over quota, and a Quotas and Rate Limits page in the cluster settings for the defaults of new projects; HopsFS quotas are now labelled GB instead of TB, the unit they always had.
  Default RonDB quotas in `hopsworks.variables.rondb_quotas` now apply to new projects; usage refreshes every `hopsworks.variables.rondb_usage_cache_ttl_seconds`, and on a large cluster raise `rondb_usage_query_timeout_seconds` if usage stops showing.
- <span class="hops-release-tag">FRONTEND</span> Fixed adding a member to a project failing when feature groups are shared with that project.
- <span class="hops-release-tag">FRONTEND</span> Fixed environment variables set in an app's configuration not reaching the app, and not showing when you edit the app.
- <span class="hops-release-tag">FRONTEND</span> Fixed web terminals shutting down after about 30 minutes in a background browser tab or on a sleeping laptop; a terminal now stops only when its session expires.
- <span class="hops-release-tag">FRONTEND</span> Fixed giving project members access to Superset failing with HTTP 429 (Too Many Requests) errors.
- <span class="hops-release-tag">FRONTEND</span> Fixed project membership changes being slow, and Airflow requests timing out or failing with server errors, when Airflow is turned off (`global._hopsworks.airflow.enabled: false`).
- <span class="hops-release-tag">FRONTEND</span> Fixed project pages showing a "Feature not enabled: DATA_SCIENCE_PROFILE" error when the data science profile is off; the model registry, deployments and apps are now hidden from the menu.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Fixed Superset redirecting to its internal host behind a reverse proxy that rewrites the Host header; set `hopsworks.variables.hopsworks_public_proxy_url` to the public `host[:port]`.
- <span class="hops-release-tag">HELM</span> Added a lifecycle webhook that posts user, project and membership events to `hopsworks.variables.lifecycle_webhook_url`, tagged with `lifecycle_webhook_cluster_id` and signed with HMAC-SHA256 when `lifecycle_webhook_secret` is set.
- <span class="hops-release-tag">HELM</span> Fixed environment builds against the managed Docker registry ignoring `hopsworks.variables.docker_operations_push_insecure` and failing on a self-signed certificate; set the new `hopsworks.variables.docker_operations_registry_http` to `"true"` for a registry served over plain HTTP.
- <span class="hops-release-tag">HELM</span> Fixed database initialization and migrations starting while RonDB was still restarting; they now wait up to 30 minutes for each RonDB StatefulSet to roll out.
- <span class="hops-release-tag">HELM</span> Added support for Kubernetes 1.35 and 1.36.
- <span class="hops-release-tag">HELM</span> Fixed model deployments with images from the managed Docker registry failing to start.
- Addressed vulnerabilities in the MySQL database of Superset.

### Breaking changes

- <span class="hops-release-tag">API</span> In the Python client, `create_feature_monitoring` no longer takes `feature_name` and `create_statistics_monitoring` is now `create_scheduled_statistics`, which takes `feature_names`, on feature groups and feature views, because one configuration now monitors several features, each named with `compare_on(feature_name=...)`.
  Also replace `with_reference_value(value)` with `compare_on(..., specific_value=value)` and `run_job()` with `run_once()`, pass `strict` and `relative` to `compare_on` by name, and read the results of each feature from `feature_statistics_results`; see [Feature monitoring](https://docs.hopsworks.ai/5.0/user_guides/fs/feature_group/feature_monitoring/).
- <span class="hops-release-tag">HELM</span> Feature monitoring is now on by default, instead of off, and the upgrade writes `hopsworks.variables.enable_feature_monitoring` to the cluster, replacing a value set in the admin UI.
  To keep it off, set it to `'false'` before you upgrade.

### Deprecations

- <span class="hops-release-tag">API</span> Deprecated the alert statuses `feature_monitor_shift_undetected` and `feature_monitor_shift_detected` in the Python client for `monitoring_shift_undetected` and `monitoring_shift_detected`.
- <span class="hops-release-tag">API</span> Deprecated `stddev` of `FeatureDescriptiveStatistics` and `FeatureTransformationStatistics` in the Python client, replaced by `std_dev`.

### Upgraded components

| Component | 5.0.0 | 5.0.1 |
| --------- | ----- | ----- |
| Base environment images | 5.0.0 | 5.0.1 |
| HopsFS | 3.4.3.1-EE-RC0 | 3.4.3.1-EE-RC1 |
| Model serving webhook | 5.0.0 | 5.0.1 |
| RonDB MySQL metrics exporter | 0.11.4 | 0.11.5 |
| Superset MySQL | 8.4.5-debian-12-r0-h1 | 8.4.5-debian-12-r0-h2 (MySQL 8.4.10) |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.mysql.exporter.maxOpenConnections` (default `1`), the connections of the RonDB MySQL metrics exporter; the render fails if it exceeds `rondb.rondb.mysql.exporter.maxUserConnections`.
- <span class="hops-release-tag">HELM</span> Added `hive.externalLoadBalancer.enabled` (default `false`) to expose the Hive Metastore outside Kubernetes, for example to external Spark applications.
- <span class="hops-release-tag">HELM</span> Added `kserve.kserve.servingruntime.vllmopenai.imageRegistry` and `kserve.kserve.servingruntime.vllmomni.imageRegistry` to pull the vLLM images from a mirror.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.velero.backup.storageLocation.s3ForcePathStyle` for S3-compatible backup stores that need path-style addressing.
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.rondbConfig.UseTcInRRGroup` and `rondb.rondb.rondbConfig.MaxRRGroupSize` to tune the RonDB round-robin groups.

### Python client

<div class="hops-client-notes" data-versions="5.0.1" markdown>

The release notes of hopsworks-api [5.0.1](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.1).

</div>

## 5.0.0 { #hopsworks-5-0-0 }

Helm chart 5.0.0 · Released on 2026-06-11 · Changes since 4.8.2 · Python client [5.0.0](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.0)

Entries marked with a 4.8 release also shipped in that 4.8 patch release, so you can skip them if you upgrade from it or a later one.
If you run 4.8.13 or later, or use Airflow with more than one MySQL server, upgrade to the latest 5.0 release instead of 5.0.0.

### What's changed

- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Agent Deployments, the Python deployments without a model, listed under **Agents** and with optional OpenTelemetry tracing into project feature groups, created in the Python client with `ModelServing.deploy_agent`; see [Agent Deployments](https://docs.hopsworks.ai/5.0/user_guides/agents/deployments/).
  Size the tracing sidecar with `hopsworks.variables.agent_deployment_otel_cpu` and `agent_deployment_otel_memory_mb`, or turn tracing off for the cluster with `agent_deployment_otel_enabled: "false"`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Apps, Streamlit dashboards and custom web apps such as Flask, FastAPI or Gradio that run as long-lived services in a project, on the new `python-app-pipeline` environment, also managed in the Python client with `Project.get_app_api()`; see [Apps](https://docs.hopsworks.ai/5.0/user_guides/projects/apps/).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added SAP HANA, Databricks Unity Catalog (on AWS, with a personal access token or OAuth machine-to-machine credentials), MongoDB and Oracle data sources, which the Python client can also create with `FeatureStore.create_data_source`; see [Data sources](https://docs.hopsworks.ai/5.0/user_guides/fs/data_source/).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added a `Users` dataset with a private home directory per member, where Jupyter, Git and the web terminal start; a Data Owner can add it to an existing project, and it can't be shared; `Project.home_path` in the Python client gives your home directory.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added logical dates and data intervals to job schedules, which jobs read from `HOPS_LOGICAL_DATE`, `HOPS_START_TIME` and `HOPS_END_TIME`, with catch-up of missed intervals, and backfill runs with `Job.run(start_time=..., end_time=...)` in the Python client; see [Schedule a job](https://docs.hopsworks.ai/5.0/user_guides/projects/jobs/schedule_job/).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added account-level environment variables, encrypted and injected into every job, deployment, app, Jupyter server and terminal you start, optionally from one of your secrets or shared with a project, and managed in the Python client with `hopsworks.get_env_vars_api()`; see [Account-level environment variables](https://docs.hopsworks.ai/5.0/user_guides/projects/env_vars/create/).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added the `python-agent-pipeline` environment, with FastAPI, LangChain, LlamaIndex and OpenTelemetry, for Python agents.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added public links to Streamlit apps (`App.make_public()` in the Python client), which open without a Hopsworks login, when `hopsworks.variables.streamlit_sharing` is `true`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added request metrics to apps: an Envoy sidecar measures requests and latency, shown with CPU, memory and Kubernetes status on the app page and in the new App Metrics Grafana dashboard.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added the datasets shared with a project to the HopsFS mount of Jupyter, jobs, apps and terminals, under `/hopsfs/shared-datasets/<project>/<dataset>`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added the `hops` CLI to the Python client package, to work with feature groups, feature views, training datasets, models, deployments, agents, apps, jobs and data sources from a shell, with browser sign-in that creates an API key for the project you pick.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added an optional transform script to dltHub ingestion jobs (`transform_script_path` of `SinkJobConfiguration` in the Python client), which now run in the `dlthub-ingestion-pipeline` environment, and memory and CPU recommendations to the ingestion forms; see [Ingest data with dltHub](https://docs.hopsworks.ai/5.0/user_guides/fs/feature_group/ingest_with_dlthub/).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added downloading models from Hugging Face into the Model Registry from the UI or with `ModelRegistry.hf_download` in the Python client; see [Import a model from Hugging Face](https://docs.hopsworks.ai/5.0/user_guides/mlops/registry/import_huggingface/).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added deleting a feature group together with the feature views that use it (`deleteFeatureViews`), or with `force` while keeping them, and removing a feature group or feature store shared with your project without deleting the original.
- <span class="hops-release-tag">API</span> Added a choice of a hash or an ordered index for the primary key of online feature groups (`primary_key_index_type` in `OnlineConfig`).
- <span class="hops-release-tag">API</span> Added lookback windows to point-in-time joins (`lookback` of feature view reads and training data) and extra filters to batch data reads of feature views (`extra_filter` of `get_batch_data`) (also in 4.8.10).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added the `vllm-omni` serving runtime and a choice of vLLM version for each deployment (`vllm_image_tag` in the Python client), and moved the `vllm-openai` runtime to the `vllm/vllm-openai` image (also in 4.8.7).
- <span class="hops-release-tag">API</span> Changed inserts into online feature groups to write only the latest row of each primary key, by event time, to the online store, and to skip rows whose TTL has already expired; pass `online_ingestion_options={"mark_online_rows": False}` to write every row.
- <span class="hops-release-tag">API</span> Added `operation="insert"` for DELTA feature groups, which appends the rows without merging them on the primary key, and stopped DELTA inserts from failing on rows with duplicate primary keys and event times.
- <span class="hops-release-tag">API</span> Added built-in model-dependent transformation functions: `log_transform`, `equal_width_binner`, `equal_frequency_binner`, `quantile_binner`, `top_k_categorical_binner`, `quantile_transformer`, `rank_normalizer`, `winsorize`, `impute_mean`, `impute_median`, `impute_constant`, `impute_mode` and `impute_category`.
- <span class="hops-release-tag">API</span> Added support for Great Expectations 1.x, up to 1.17.1, and Polars 1.x, up to 1.40.1, to the Python client.
- <span class="hops-release-tag">API</span> Added Spark Connect support to the Python client, as used by Spark web terminals; there statistics are computed with pandas and HUDI feature groups can't be read or written.
- <span class="hops-release-tag">API</span> Added a cache to `Model.download()` in the Python client, which reuses a downloaded model version across sessions; free it with `Model.clear_cache()`.
- <span class="hops-release-tag">API</span> Added `Project.get_superset_api()` to the Python client to manage Superset datasets, charts and dashboards.
- <span class="hops-release-tag">API</span> Added `namespace` to `hopsworks.create_project` to choose the Kubernetes namespace of a new project.
- <span class="hops-release-tag">API</span> Changed the Python client to stop sending usage data unless the environment variable `ENABLE_HOPSWORKS_USAGE` is `true`.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added environment variables to deployments (`env_vars` in the Python client) (also in 4.8.6).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Added uploading a file as a secret (`create_secret_from_file` in the Python client) (also in 4.8.9).
- <span class="hops-release-tag">API</span> Added `source=opensearch` to the deployment logs API, to read the logs of stopped deployments.
- <span class="hops-release-tag">API</span> Fixed `hopsworks.login()` from outside the cluster failing when model serving has no Istio load balancer.
- <span class="hops-release-tag">API</span> Fixed dltHub ingestion jobs created from the Python client failing on source columns whose names the feature store changes, such as names with upper-case letters.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Addressed vulnerabilities in the feature store.
- <span class="hops-release-tag">API</span> Added the `TIMESTAMP(n)` and `DATETIME(n)` online types with fractional seconds (also in 4.8.7).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">HELM</span> Changed job runs to fail with HTTP 429 when the user already has 10 queued executions of the job (cluster variable `quotas_max_queued_executions_per_user_per_job`, `-1` turns the limit off).
- <span class="hops-release-tag">API</span> Changed ArrowFlight queries to read only the partitions and files that match their filters (also in 4.8.3).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Changed Python deployments without a model to reject request batching and inference logging, which they don't support.
- <span class="hops-release-tag">API</span> Fixed a failed or concurrent feature group creation leaving a Hive table behind that blocked creating the feature group again.
- <span class="hops-release-tag">API</span> Fixed new feature group and training dataset locations naming a NameNode pod IP (also in 4.8.7).
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Fixed model deployments being rejected on OpenShift by the `restricted-v2` security context constraint.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Agent Tasks, jobs that run Claude Code or OpenAI Codex headless on a prompt, in the new `agent-job` environment; turn them off with `hopsworks.variables.agent_jobs_enabled: "false"`, see [Agent Tasks](https://docs.hopsworks.ai/5.0/user_guides/agents/tasks/).
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added the Wizard, which opens on an empty project, asks what to build and from which data, and hands a kickoff prompt to Claude Code in the web terminal; it can import the feature groups of the `hopsworks_default` project (cluster variable `default_featurestore_project_name`).
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Spark and GPU web terminals, and Claude Code, OpenAI Codex, the `hops` CLI and a `kubectl` limited to the project in every terminal; with `hopsworks.terminal.enabled`, the chart also pushes the terminal images.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added Platform Intelligence, which uses an LLM to suggest feature group metadata and ingestion schedules from a data source preview, generate vLLM configurations and fill in model deployments; set `hopsworks.variables.platform_intelligence_llm_api_key`, and optionally `platform_intelligence_llm_model` and `platform_intelligence_llm_base_url`, to turn it on.
- <span class="hops-release-tag">FRONTEND</span> Added per-project DAG isolation and the last runs of each DAG on the Airflow page; see [Airflow 3 in Hopsworks](https://docs.hopsworks.ai/5.0/user_guides/projects/airflow/airflow3_upgrade/).
- <span class="hops-release-tag">FRONTEND</span> Added an optional expiration date to API keys; see [Create an API key](https://docs.hopsworks.ai/5.0/user_guides/projects/api_key/create_api_key/).
- <span class="hops-release-tag">FRONTEND</span> Added a Superset dashboards page to projects, to make dashboards public, share them with other projects, create permalinks and delete them; see [Superset](https://docs.hopsworks.ai/5.0/user_guides/projects/superset/superset/).
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Added a Superset Public role to share dashboards and chart permalinks without a login, and enabled dashboard role-based access control.
  Only dashboards granted to the Public role are public; set `superset.superset.allowAnonymousAccess: false` to require a login for all.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Changed starting a Jupyter server, terminal or app to fail with HTTP 503 when the cluster is at its session limit, and added session capacity badges to those pages and WebSocket proxy panels to the Hopsworks Grafana dashboard; see [WebSocket proxy pool](https://docs.hopsworks.ai/5.0/setup_installation/admin/monitoring/websocket-pool/).
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Changed web terminal sessions to end after 4 hours instead of 8 (cluster variable `terminal_session_hours`).
- <span class="hops-release-tag">FRONTEND</span> Changed the error pages of the Hopsworks server from the Payara defaults to Hopsworks pages.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Fixed downloading Superset charts through Hopsworks, and the Hopsworks proxy passing only 5 Superset requests at a time; tune its connection pool and timeouts with `hopsworks.variables.superset_proxy_*`.
- <span class="hops-release-tag">FRONTEND</span> Fixed BigQuery previews failing for SQL queries and for BigQuery projects with a hyphen in their name, previews scanning the whole table, and the **Materialization Dataset** error staying after **BigQuery Project** was filled.
- <span class="hops-release-tag">FRONTEND</span> Fixed feature store metrics counting Hudi metadata columns as features (also in 4.8.9).
- <span class="hops-release-tag">FRONTEND</span> Changed the Hopsworks UI to a new design, with a regrouped project sidebar.
- <span class="hops-release-tag">FRONTEND</span> Added drag-and-drop upload of files and folders to the file browser.
- <span class="hops-release-tag">FRONTEND</span> Fixed the UI hanging when a web terminal failed to start.
- <span class="hops-release-tag">FRONTEND</span> Fixed adding an expectation to an expectation suite in the UI.
- <span class="hops-release-tag">FRONTEND</span> Fixed the pagination of the feature usage table (also in 4.8.9).
- <span class="hops-release-tag">FRONTEND</span> Fixed libhdfs in Jupyter not finding HopsFS with a custom Consul domain (also in 4.8.7).
- <span class="hops-release-tag">FRONTEND</span> Fixed web terminals failing to pull their image from a registry that needs credentials.
- <span class="hops-release-tag">HELM</span> Added `ddl-mysqld`, a separate MySQL server for online feature store schema changes, enabled by default with `rondb.rondb.meta.ddlMySQLd.enabled` (also in 4.8.4).
- <span class="hops-release-tag">HELM</span> Added Kyverno policy exceptions so apps and web terminals start under Kyverno policies (`hw-kyverno.policyExceptions.pythonapp.enabled`, `hw-kyverno.policyExceptions.terminal.enabled`).
- <span class="hops-release-tag">HELM</span> Added CPU and memory requests and limits to the initialization step of Spark and Ray jobs, tuned with `hopsworks.variables.kube_scheduling_jobinit_*`, and to Git operation pods, so namespace quotas count them.
- <span class="hops-release-tag">HELM</span> Fixed external LoadBalancer Services, such as those of ArrowFlight, HopsFS, OpenSearch and Trino, not being created with Helm 3.20.1 and later; a component without its own setting again follows `global._hopsworks.externalLoadBalancers`.
- <span class="hops-release-tag">HELM</span> Changed RonDB upgrades to roll the management server first, so data nodes no longer shut down with error 2305, and to fail when `rondb.rondb.clusterSize.numNodeGroups` differs from the running cluster, which RonDB can't change online (also in 4.8.6).
- <span class="hops-release-tag">HELM</span> Fixed fresh installs failing and rolling back when the Knative Serving webhook was not ready yet.
- <span class="hops-release-tag">HELM</span> Fixed `helm upgrade --force` failing with a conflict on unchanged chart Jobs, which now run again on every `helm upgrade` (also in 4.8.3).
- <span class="hops-release-tag">HELM</span> Added asynchronous upload of HopsFS blocks to object storage (`hopsfs.objectStorage.asyncUpload.enabled`, off by default).
  With it on, a stopping DataNode first uploads its pending blocks, which can take about an hour (`hopsfs.datanode.gracefulDrain`); turning that drain off can lose them.
- <span class="hops-release-tag">HELM</span> Added Grafana dashboards and Prometheus alerts for the MySQL and Redis of Superset, and capped the Superset Redis memory at 256 MB with `allkeys-lru` eviction.
- <span class="hops-release-tag">HELM</span> Changed the HopsFS DataNode PodDisruptionBudget to evict one DataNode at a time, and HopsFS pods to restart when a `helm upgrade` changes the HopsFS configuration.
- <span class="hops-release-tag">HELM</span> Changed the console logs of the HopsFS NameNode and DataNodes from JSON to plain text.
- <span class="hops-release-tag">HELM</span> Fixed the image push Job not running again under Argo CD when only image versions or digests changed (also in 4.8.6).
- <span class="hops-release-tag">HELM</span> Fixed Airflow pods failing to start when the HopsFS mount took longer than 30 seconds.
- Addressed vulnerabilities in the OpenSearch image.
- Addressed vulnerabilities in the Payara image (also in 4.8.10).
- Addressed vulnerabilities in the Strimzi images and the Redis image of Superset (also in 4.8.6).
- Addressed vulnerabilities in the Grafana, OpenSearch Dashboards and remote shuffle service images, and in the MySQL image of Superset (also in 4.8.11).

### Breaking changes

- <span class="hops-release-tag">API</span> In a job with a schedule, `read()` of a feature group with an event time now reads only the rows of the run's data interval, from `HOPS_START_TIME` to `HOPS_END_TIME`, instead of the whole feature group, when it's called without `start_time`, `end_time` or `wallclock_time`.
  Pass `start_time` and `end_time` where a scheduled job must read more; see [Schedule a job](https://docs.hopsworks.ai/5.0/user_guides/projects/jobs/schedule_job/).
- <span class="hops-release-tag">API</span> `create_feature_group` in the Python client now creates a DELTA feature group by default, and fails when the `deltalake` package is missing, instead of falling back to HUDI; `time_travel_format=None` now creates a feature group without time travel, instead of choosing the default.
  Set `time_travel_format` to `"DELTA"` or `"HUDI"` explicitly where you relied on the old behaviour.
- <span class="hops-release-tag">API</span> `Model.save` in the Python client now moves model files saved from a path under `/mnt/hopsfs` into the model registry, instead of uploading a copy and keeping the originals; pass `keep_original_files=True` to keep them.
- <span class="hops-release-tag">API</span> The `disable_online_ingestion_count` write option of feature group inserts is now read from `online_ingestion_options` in the Python and Java clients, so a top-level `disable_online_ingestion_count` is ignored; move it into `online_ingestion_options`.
- <span class="hops-release-tag">API</span> The Python client no longer supports Python 3.9; use Python 3.10 to 3.13, see the [client installation guide](https://docs.hopsworks.ai/5.0/user_guides/client_installation/).
- <span class="hops-release-tag">API</span> The Python client now prefixes its internal functions and methods, those not in the API reference, with an underscore, such as `hopsworks_common.client.get_instance()`, which is now `_get_instance()`, so code that calls them fails.
  Use the documented API instead.
- <span class="hops-release-tag">API</span> <span class="hops-release-tag">FRONTEND</span> Hopsworks now deletes finished job executions and their logs 90 days after they end, instead of keeping them, and deletes the older ones as soon as it starts after the upgrade.
  Copy the logs you want to keep before you upgrade; the cluster variable `executions_ttl_days` sets the retention (`0` turns it off) and takes effect when Hopsworks restarts.
- <span class="hops-release-tag">FRONTEND</span> <span class="hops-release-tag">HELM</span> Upgraded Airflow from 1.10 to Airflow 3, and DAGs written for Airflow 1.10 don't run on it.
  The upgrade drops and recreates the Airflow metadata database, so DAG run history and the Variables and Connections created in Airflow are lost for good; see [Airflow 3 in Hopsworks](https://docs.hopsworks.ai/5.0/user_guides/projects/airflow/airflow3_upgrade/).
- <span class="hops-release-tag">HELM</span> Removed `hopsworks.variables.dlthub_image_name`, replaced by the `dlthub-ingestion-pipeline` environment, and `hopsworks.payara.executorService`, replaced by `hopsworks.payara.websocketProxy`.
  Remove them from your values before you upgrade, because the values schema rejects them.
- <span class="hops-release-tag">HELM</span> `global._hopsworks.superset.mysql.enabled` and `global._hopsworks.superset.redis.enabled` must match `superset.mysql.enabled` and `superset.superset.redis.enabled`, which used to be enough on their own.
  If you disabled the MySQL or Redis of Superset, also set the matching global value to `false`, or the chart fails to render.
- <span class="hops-release-tag">HELM</span> The Spark operator pre-upgrade Job now pulls the `spark-operator-crds` image (`spark.sparkOperatorUpgradeJob.crdImage`) on every upgrade, instead of the toolbox image (also in 4.8.17).
  Air-gapped installations must mirror it first, or `helm upgrade` fails.
- <span class="hops-release-tag">HELM</span> `hopsworks.variables.jupyter_shell_command` is now a JSON list of the command and its arguments, instead of a path.
  If you set it, rewrite it as a list, for example `'["/bin/bash"]'`, because a plain path produces an invalid Jupyter configuration.
- <span class="hops-release-tag">HELM</span> Removed `VLLM_USE_FLASHINFER_SAMPLER=0` from the vLLM serving runtimes (also in 4.8.7).
  If you serve vLLM models on GPUs older than compute capability 7.5, set it in `kserve.kserve.servingruntime.vllmopenai.env` or `kserve.kserve.servingruntime.vllmomni.env`.

### Deprecations

- <span class="hops-release-tag">HELM</span> Deprecated `airflow.reset_db_if_error`, which Airflow 3 ignores.
- <span class="hops-release-tag">HELM</span> Deprecated `hopsfs.datanode.podDisruptionBudget.minAvailable`, which the DataNode PodDisruptionBudget no longer reads.

### Upgrade steps

No upgrade steps are listed for this release.

### Upgraded components

| Component | 4.8.2 | 5.0.0 |
| --------- | ----- | ----- |
| Airflow image | 4.8.0 | 5.0.0 |
| ArrowFlight server | 4.8.0 | 5.0.0 |
| Base environment images | 4.8.1 | 5.0.0 |
| Git image | 1.3 | 1.4 |
| Grafana | 10.2.2-h3 | 10.2.2-h6 (also in 4.8.11) |
| Hive Metastore | 3.0.0.13.15 | 3.0.0.14.5-v1 |
| HopsFS | 3.2.0.19-EE-RC0 | 3.4.3.1-EE-RC0 |
| Inference logger | 4.8.0 | 5.0.0 |
| Model serving authenticator | 4.8.0 | 5.0.0 |
| Model serving webhook | 4.8.0 | 5.0.0 |
| OnlineFS | 2.2 | 2.3 |
| OpenSearch | 1.3.6.1 | 1.3.6.2 |
| OpenSearch Dashboards | 1.3.6.3 | 1.3.6.6 (also in 4.8.11) |
| Payara | 6.2024.12-jdk17.2 | 6.2024.12-jdk17.6 |
| Python in the base environments | 3.11 | 3.12 |
| Ray | 2.9.0 | 2.55.1 |
| Remote shuffle service (Apache Uniffle) | 0.10.5 | 0.10.7 (also in 4.8.11) |
| RonDB | 26.02.2 | 26.02.4 (also in 4.8.11) |
| scikit-learn model server | 0.14.0 | 0.14.1 |
| Spark | 3.5.5 | 3.5.5.1 |
| Spark history server | 3.5.5 | 3.5.5.1 |
| Storage initializer | 4.8.0 | 5.0.0 |
| Strimzi | 0.45.1-h3 | 0.45.1-h4 (also in 4.8.6) |
| Superset | 6.0.0.0 | 6.0.0p2.4 |
| Superset MySQL | 8.4.5-debian-12-r0 | 8.4.5-debian-12-r0-h1 (also in 4.8.11) |
| Superset Redis | 7.0.10-debian-11-r4 | 7.4.9-bookworm (also in 4.8.6) |
| TensorFlow | 2.17.0 | 2.19.0 |
| TensorFlow Serving | 2.17.0 | 2.19.0 |
| Toolbox image (hwutils) | 1.4 | 1.7 |
| Trino | 477-v0 | 480-v6 |
| vLLM | v0.14.0 | v0.20.0 (also in 4.8.7) |

### Configuration changes

- <span class="hops-release-tag">HELM</span> Changed the default HopsFS resources: 1024 MiB of heap instead of 500, a CPU request of 1 and a CPU limit of 4 instead of 500m and 1000m for the NameNode and DataNodes, a NameNode memory request of 1024Mi and limit of 2560Mi instead of 500Mi and 750Mi, a DataNode memory limit of 2048Mi instead of 750Mi, and 1024 MiB of NameNode direct memory instead of 500.
  Plan for the extra CPU requests of the 5 default DataNodes.
- <span class="hops-release-tag">HELM</span> Fixed `hopsfs.namenode.auditLog.enableStatOperation` and `enableListOperation` being ignored; the NameNode no longer audit-logs stat and list operations unless you set them to `true` (also in 4.8.8).
- <span class="hops-release-tag">HELM</span> Changed `hopsfs.image.registry`, when set, to override `global._hopsworks.imageRegistry` as the full registry and path prefix of the HopsFS image, ending in `/`.
- <span class="hops-release-tag">HELM</span> Added the Superset, Trino and Kueue images to `values.airgap.yaml`.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.payara.websocketProxy` to tune the WebSocket proxy of Jupyter, terminals and apps, such as `maxSessionsPerApp` (default `500` per pod).
- <span class="hops-release-tag">HELM</span> Added `hopsworks.variables.kube_user_workload_tolerations` for tolerations on user workloads such as Jupyter, jobs and deployments (also in 4.8.8).
- <span class="hops-release-tag">HELM</span> Added `hopsworks.dockerImage.packageAuth.secretName` and `caCertsConfigMap` for pip, conda and apt mirrors that need credentials or a private CA (also in 4.8.8).
- <span class="hops-release-tag">HELM</span> Added `global._hopsworks.kyverno.policies.addCertificatesVolume.initContainers.enabled` to inject the custom CA certificates into the init containers of pods annotated `kyverno-inject-certs-init: enabled` (also in 4.8.8).
- <span class="hops-release-tag">HELM</span> Added `onlinefs.configmapOverrides` to tune the threads, caches, and RonDB, Kafka and logging settings of the online feature store service.
- <span class="hops-release-tag">HELM</span> Added `hopsworks.variables.kube_serving_vllm_versions` and `kube_serving_vllm_omni_versions`, the vLLM image tags a deployment can choose; update them when you change the vLLM runtime images (also in 4.8.7).
- <span class="hops-release-tag">HELM</span> Added `rondb.rondb.createPriorityClass`, which you set to `false` when the RonDB PriorityClass already exists, for example on a satellite cluster (also in 4.8.5).
- <span class="hops-release-tag">HELM</span> Added RonDB settings such as `DataMemory` and `TimeBetweenGlobalCheckpoints` to `rondb.rondb.rondbConfig` (also in 4.8.5).
- <span class="hops-release-tag">HELM</span> Added `hopsfs.namenode.blockSizeMiB` (default `128`) to set the HopsFS block size.
- <span class="hops-release-tag">HELM</span> Added `arrowflight.deployment.server.hopsfs_query_mode` to make the ArrowFlight server load feature group data into DuckDB tables (`table`) instead of views (`view`) (also in 4.8.3).
- <span class="hops-release-tag">HELM</span> Added `BackupLogBufferSize` to `rondb.rondb.rondbConfig` (also in 4.8.6).
- <span class="hops-release-tag">HELM</span> Added `PartitionsPerNode` to `rondb.rondb.rondbConfig` (also in 4.8.11).

### Python client

<div class="hops-client-notes" data-versions="5.0.0" markdown>

The release notes of hopsworks-api [5.0.0](https://github.com/logicalclocks/hopsworks-api/releases/tag/5.0.0).

</div>

