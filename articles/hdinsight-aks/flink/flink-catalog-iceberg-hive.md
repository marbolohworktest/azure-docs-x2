---
title: Table API and SQL - Use Iceberg Catalog type with Hive in Apache Flink® on HDInsight on AKS
description: Learn how to create Iceberg Catalog in Apache Flink® on HDInsight on AKS.
ms.service: azure-hdinsight-on-aks
ms.topic: how-to
ms.date: 09/20/2024
ROBOTS: NOINDEX
---

# Create Iceberg Catalog in Apache Flink® on HDInsight on AKS

[!INCLUDE [retirement-notice](../includes/retirement-notice.md)]
[!INCLUDE [feature-in-preview](../includes/feature-in-preview.md)]


[Apache Iceberg](https://iceberg.apache.org/) is an open table format for huge analytic datasets. Iceberg adds tables to compute engines like Apache Flink, using a high-performance table format that works just like a SQL table. Apache Iceberg [supports](https://iceberg.apache.org/multi-engine-support/#apache-flink) both Apache Flink’s DataStream API and Table API.

In this article, we learn how to use Iceberg Table managed in Hive catalog, with Apache Flink on HDInsight on AKS cluster.

## Prerequisites
- You're required to have an operational Flink cluster with secure shell, learn how to [create a cluster](../flink/flink-create-cluster-portal.md)
   - Refer this article on how to use CLI from [Secure Shell](./flink-web-ssh-on-portal-to-flink-sql.md) on Azure portal.

### Add dependencies

**Script actions**

1. Upload hadoop-hdfs-client and iceberg-flink connector jar into Flink cluster Job Manager and Task Manager.
 
1. Go to Script actions on Cluster Azure portal.
 
1. Upload [hadoop-hdfs-client_jar](https://hdiconfigactions2.blob.core.windows.net/flink-script-action/hudi-sa-test.sh)

   :::image type="content" source="./media/flink-catalog-iceberg-hive/add-script-action.png" alt-text="Screenshot showing how to add script action.":::

   :::image type="content" source="./media/flink-catalog-iceberg-hive/script-action-successful.png" alt-text="Screenshot showing script action added successfully.":::

1. Once you launch the Secure Shell (SSH), let us start downloading the dependencies required to the SSH node, to illustrate the Iceberg table managed in Hive catalog.

   ```
   wget https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-flink-runtime-1.17/1.4.0/iceberg-flink-runtime-1.17-1.4.0.jar -P $FLINK_HOME/lib
   wget https://repo1.maven.org/maven2/org/apache/parquet/parquet-column/1.12.2/parquet-column-1.12.2.jar -P $FLINK_HOME/lib
   wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-hdfs-client/3.3.4/hadoop-hdfs-client-3.3.4.jar -P $FLINK_HOME
   export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$FLINK_HOME/hadoop-hdfs-client-3.3.4.jar
   ```

## Start the Apache Flink SQL Client
A detailed explanation is given on how to get started with Flink SQL Client using [Secure Shell](./flink-web-ssh-on-portal-to-flink-sql.md) on Azure portal. You're required to start the SQL Client as described on the article by running the following command. 
```
./bin/sql-client.sh
```
### Create Iceberg Table managed in Hive catalog

With the following steps, we illustrate how you can create Flink-Iceberg catalog using Hive catalog.

```sql
  CREATE CATALOG hive_catalog WITH (
  'type'='iceberg',
  'catalog-type'='hive',
  'uri'='thrift://hive-metastore:9083',
  'clients'='5',
  'property-version'='1',
  'warehouse'='abfs://container@storage_account.dfs.core.windows.net/iceberg-output');
```
> [!NOTE]
> - In the above step, the container and storage account *need not be same* as specified during the cluster creation.
> - In case you want to specify another storage account, you can update `core-site.xml` with `fs.azure.account.key.<account_name>.dfs.core.windows.net: <azure_storage_key>` using configuration management.

```sql
  USE CATALOG hive_catalog;
```

#### Add dependencies to server classpath

```sql
ADD JAR '/opt/flink-webssh/lib/iceberg-flink-runtime-1.17-1.4.0.jar';
ADD JAR '/opt/flink-webssh/lib/parquet-column-1.12.2.jar';
```
#### Create Database

```sql
  CREATE DATABASE iceberg_db_2;
  USE iceberg_db_2;
```
#### Create Table

```sql
    CREATE TABLE `hive_catalog`.`iceberg_db_2`.`iceberg_sample_2`
    (
    id BIGINT COMMENT 'unique id',
    data STRING
    )
    PARTITIONED BY (data);
```
#### Insert Data into the Iceberg Table

```sql
    INSERT INTO `hive_catalog`.`iceberg_db_2`.`iceberg_sample_2` VALUES (1, 'a');
```

#### Output of the Iceberg Table

You can view the Iceberg Table output on the ABFS container.

:::image type="content" source="./media/flink-catalog-iceberg-hive/flink-catalog-iceberg-hive-output.png" alt-text="Screenshot showing output of the Iceberg table in ABFS.":::

### Reference

- [Apache Flink Website](https://flink.apache.org/)
- Apache, Apache Hive, Hive, Apache Iceberg, Iceberg, Apache Flink, Flink, and associated open source project names are [trademarks](../trademarks.md) of the [Apache Software Foundation](https://www.apache.org/) (ASF).
