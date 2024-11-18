---
title: What's new in HDInsight on AKS? (Preview)
description: An introduction to new concepts in HDInsight on AKS that aren't in HDInsight.
ms.service: azure-hdinsight-on-aks
ms.topic: conceptual
ms.date: 09/20/2024
ROBOTS: NOINDEX
---

# What's new in HDInsight on AKS? (Preview)

[!INCLUDE [retirement-notice](includes/retirement-notice.md)]
[!INCLUDE [feature-in-preview](includes/feature-in-preview.md)]

In HDInsight on AKS, all cluster management and operations have native support for [service management](./service-configuration.md) on Azure portal for individual clusters. 

In HDInsight on AKS, two new concepts are introduced:

* **Cluster Pools** are used to group and manage clusters.
* **Clusters** are used for open source computes, they're hosted within a cluster pool. 

## Cluster Pools

HDInsight on AKS runs on Azure Kubernetes Service (AKS). The top-level resource is the Cluster Pool and manages all clusters running on the same AKS cluster. When you create a Cluster Pool, an underlying AKS cluster is created at the same time to host all clusters in the pool. Cluster pools are a logical grouping of clusters, which helps in building robust interoperability across multiple cluster types and allow enterprises to have the clusters in the same virtual network. Cluster pools provide rapid and cost-effective access to all the cluster types created on-demand and at scale.One cluster pool corresponds to one cluster in AKS infrastructure.

## Clusters

Clusters are individual open source compute workloads, such as Apache Spark, Apache Flink, and Trino, which can be created rapidly in few minutes with preset configurations and few clicks. Though running on the same cluster pool, each cluster can have its own configurations, such as cluster type, version, node VM size, node count. Clusters are running on separated compute resources with its own DNS and endpoints.

## Features currently in preview

The following table list shows the features of HDInsight on AKS that are currently in preview. Preview features are sorted alphabetically.

|	Area |	Features	|	
|	---	|	---	|
|	Fundamentals	|	[Create Pool and clusters](./quickstart-create-cluster.md) using portal,	Web secure shell (ssh) support, Ability to Choose number of worker nodes during cluster creation	|	
|	Storage	|	ADLS Gen2 Storage [support](./cluster-storage.md)	|	
|	Metastore	|	External Metastore support for [Trino](./trino/trino-connect-to-metastore.md), [Spark](./spark/use-hive-metastore.md) and [Flink](./flink/use-hive-metastore-datastream.md),	Integrate with [HDInsight](overview.md#connectivity-to-hdinsight)|		
|	Security	|	Support for ARM RBAC,	Support for MSI based authentication,	Option to provide [cluster access](./hdinsight-on-aks-manage-authorization-profile.md) to other users	|	
|	Logging and Monitoring	|	Log aggregation in Azure [log analytics](./how-to-azure-monitor-integration.md), for server logs,	Cluster and Service metrics via [Managed Prometheus and Grafana](./monitor-with-prometheus-grafana.md),	Support server metrics in [Azure monitor](/azure/azure-monitor/overview),	Service Status page for monitoring the [Service health](./service-health.md)	|	
|	Auto Scale	|	Load based [Auto Scale](hdinsight-on-aks-autoscale-clusters.md#create-a-cluster-with-load-based-auto-scale), and Schedule based [Auto Scale](hdinsight-on-aks-autoscale-clusters.md#create-a-cluster-with-schedule-based-auto-scale) |
|	Customize and Configure Clusters	|	Support for [script actions](./manage-script-actions.md) during cluster creation, Support for [library management](./spark/library-management.md), [Service configuration](./service-configuration.md) settings after cluster creation	|	
|	Trino	|	Support for [Trino catalogs](./trino/trino-add-catalogs.md), [Trino CLI Support](./trino/trino-ui-command-line-interface.md), [DBeaver](./trino/trino-ui-dbeaver.md) support for query submission,	Add or remove [plugins](./trino/trino-custom-plugins.md) and [connectors](./trino/trino-connectors.md), Support for [logging query](./trino/trino-query-logging.md) events, Support for [scan query statistics](./trino/trino-scan-stats.md) for any [Connector](./trino/trino-connectors.md) in Trino dashboard, Support for Trino [dashboard](./trino/trino-ui.md) to monitor queries, [Query Caching](./trino/trino-caching.md), Integration with Power BI, Integration with [Apache Superset](./trino/trino-superset.md), Redash, Support for multiple [connectors](./trino/trino-connectors.md) |
|	Flink	|	Support for Flink native web UI, Flink support with HMS for [DStream](./flink/use-hive-metastore-datastream.md), Submit jobs to the cluster using [REST API and Azure portal](./flink/flink-job-management.md), Run programs packaged as JAR files via the [Flink CLI](./flink/use-flink-cli-to-submit-jobs.md), Support for persistent Savepoints, Support for update the configuration options when the job is running,  Connecting to multiple Azure services: [Azure Cosmos DB](./flink/cosmos-db-for-apache-cassandra.md), [Azure Databricks](./flink/azure-databricks.md), [Azure Data Explorer](./flink/integration-of-azure-data-explorer.md), [Azure Event Hubs](./flink/flink-how-to-setup-event-hub.md), [Azure IoT Hub](./flink/azure-iot-hub.md), [Azure Pipelines](./flink/use-azure-pipelines-to-run-flink-jobs.md), [Azure Data Factory Workflow Orchestration Manager](./flink/flink-job-orchestration.md), [HDInsight Kafka](./flink/process-and-consume-data.md), Submit jobs to the cluster using [Flink CLI](./flink/use-flink-cli-to-submit-jobs.md) and [CDC](./flink/monitor-changes-postgres-table-flink.md) with Flink |
|	Spark	|	[Jupyter Notebook](./spark/submit-manage-jobs.md), Support for [Delta lake](./spark/azure-hdinsight-spark-on-aks-delta-lake.md) 2.0, Zeppelin Support, Support ATS, Support for Yarn History server interface, Job submission using SSH, Job submission using SDK and [Machine Learning Notebook](./spark/azure-hdinsight-spark-on-aks-delta-lake.md)	|		

## Roadmap of Features

|	Feature	|	Estimated release timeline	| Status |
| --- | --- | --- |
|	[Autoscale - Load Based - Trino](./hdinsight-on-aks-autoscale-clusters.md)	|	Q1 2024	| Completed |
|	Shuffle aware load based auto scale for Spark	|	Q2 2024	| In Progress |
|	[In Place Upgrade](./in-place-upgrade.md)	|	Q2 2024	| Completed  |
|	Reserved Instance Support 	|	Q2 2024	| In Progress |
|	MSI based authentication for Metastore (SQL)	|	Q1 2024	| In Progress |
|	Spark 3.4	|	Q2 2024	| In Progress |
|	Trino 426 |	Q1 2024	| Completed |
|	Ranger for RBAC	|	Q2 2024	| In Progress |
|	[App mode support for Flink](./flink/application-mode-cluster-on-hdinsight-on-aks.md)	|	Q1 2024	| Completed |
|	Flink 1.17 	|	Q1 2024	| Completed |
|	Spark ACID Support	|	Q1 2024	| In Progress|
|	Configurable SKUs for Headnode, SSH	|	Q2 2024	| In Progress|
|	[Flink SQL Gateway Support](./flink/start-sql-client-cli-gateway-mode.md)	|	Q1 2024	| Completed |
|	[Private Clusters for HDInsight on AKS](./control-egress-traffic-from-hdinsight-on-aks-clusters.md)	|	Q1 2024	| Completed |
|	Ranger Support for Spark SQL	|	Q4 2024	| In Progress |
|	Ranger ACLs on Storage Layer	|	Q4 2024	| In Progress |
|	Support for One lake as primary container	|	Q2 2024	| In Progress|
