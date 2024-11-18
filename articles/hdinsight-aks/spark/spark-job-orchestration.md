---
title: Azure Data Factory Workflow Orchestration Manager (powered by Apache Airflow) with Apache Spark® on HDInsight on AKS
description: Learn how to perform Apache Spark® job orchestration using Azure Data Factory Workflow Orchestration Manager
ms.service: azure-hdinsight-on-aks
ms.topic: how-to
ms.date: 09/20/2024
ROBOTS: NOINDEX
---

# Apache Spark® job orchestration using Azure Data Factory Workflow Orchestration Manager (powered by Apache Airflow)

[!INCLUDE [retirement-notice](../includes/retirement-notice.md)]
[!INCLUDE [feature-in-preview](../includes/feature-in-preview.md)]


This article covers managing a Spark job using [Apache Spark Livy API](https://livy.incubator.apache.org/docs/latest/rest-api.html) and orchestration data pipeline with Azure Data Factory Workflow Orchestration Manager. [Azure Data Factory Workflow Orchestration Manager](/azure/data-factory/concepts-workflow-orchestration-manager) service is a simple and efficient way to create and manage [Apache Airflow](https://airflow.apache.org/) environments, enabling you to run data pipelines at scale easily.

Apache Airflow is an open-source platform that programmatically creates, schedules, and monitors complex data workflows. It allows you to define a set of tasks, called operators that can be combined into directed acyclic graphs (DAGs) to represent data pipelines. 

The following diagram shows the placement of Airflow, Key Vault, and HDInsight on AKS in Azure. 

:::image type="content" source="./media/spark-job-orchestration/spark-airflow-azure.png" alt-text="Screenshot shows the placement of airflow, key vault, and HDInsight on AKS in Azure." lightbox="./media/spark-job-orchestration/spark-airflow-azure.png":::

Multiple Azure Service Principals are created based on the scope to limit the access it needs and to manage the client credential life cycle independently. 

It is recommended to rotate access keys or secrets periodically (you can use  various [design pattern’s](/azure/key-vault/secrets/tutorial-rotation-dual?tabs=azure-cli) to rotate secrets). 

## Setup steps 

1. [Setup Spark Cluster](create-spark-cluster.md)

1. Upload your Apache Spark Application jar to the storage account. It can be the primary storage account associated with the Spark cluster or any other storage account, where you should assign the "Storage Blob Data Owner" role to the user-assigned MSI used for the cluster in this storage account.

1. Azure Key Vault - You can follow [this tutorial to create a new Azure Key Vault](/azure/key-vault/general/quick-create-portal/) in case, if you don't have one. 

1. Create [Microsoft Entra service principal](/cli/azure/ad/sp/) to access Key Vault – Grant permission to access Azure Key Vault with the “Key Vault Secrets Officer” role, and make a note of ‘appId’ ‘password’, and ‘tenant’ from the response. We need to use the same for Airflow to use Key Vault storage as backends for storing sensitive information. 

    ```
    az ad sp create-for-rbac -n <sp name> --role “Key Vault Secrets Officer” --scopes <key vault Resource ID> 
    ```


1. Enable [Azure Key Vault for Workflow Orchestration Manager](/azure/data-factory/enable-azure-key-vault) to store and manage your sensitive information in a secure and centralized manner. By doing this, you can use variables and connections, and they automatically be stored in Azure Key Vault. The name of connections and variables need to be prefixed by variables_prefix  defined in AIRFLOW__SECRETS__BACKEND_KWARGS. For example, If variables_prefix has a value as  hdinsight-aks-variables then for a variable key of hello, you would want to store your Variable at hdinsight-aks-variable -hello.

    - Add the following settings for the Airflow configuration overrides in integrated runtime properties: 

      - AIRFLOW__SECRETS__BACKEND: 
      `"airflow.providers.microsoft.azure.secrets.key_vault.AzureKeyVaultBackend"` 

      - AIRFLOW__SECRETS__BACKEND_KWARGS:  
      `"{"connections_prefix": "airflow-connections", "variables_prefix": "hdinsight-aks-variables", "vault_url": <your keyvault uri>}”` 

    - Add the following setting for the Environment variables configuration in the Airflow integrated runtime properties: 

      - AZURE_CLIENT_ID = `<App Id from Create Azure AD Service Principal>` 

      - AZURE_TENANT_ID = `<Tenant from Create Azure AD Service Principal> `

      - AZURE_CLIENT_SECRET = `<Password from Create Azure AD Service Principal> `

      Add Airflow requirements - [apache-airflow-providers-microsoft-azure](https://airflow.apache.org/docs/apache-airflow-providers-microsoft-azure/stable/index.html) 

      :::image type="content" source="./media/spark-job-orchestration/airflow-configuration-environment-variable.png" alt-text="Screenshot shows airflow configuration and environment variables." lightbox="./media/spark-job-orchestration/airflow-configuration-environment-variable.png":::

 
1. Create [Microsoft Entra service principal](/cli/azure/ad/sp/) to access HDInsight on AKS cluster Azure – [Grant access to HDInsight AKS Cluster](/azure/hdinsight-aks/hdinsight-on-aks-manage-authorization-profile#how-to-grant-access), make a note of appId, password, and tenant from the response. 

    `az ad sp create-for-rbac -n <sp name>` 

1. Create the following secrets in your key vault with the value from the previous AD Service principal appId, password, and tenant, prefixed by property variables_prefix defined in AIRFLOW__SECRETS__BACKEND_KWARGS. The DAG code can access any of these variables without variables_prefix.  

    - hdinsight-aks-variables-api-client-id=`<App ID from previous step> `

    - hdinsight-aks-variables-api-secret=`<Password from previous step> `

    - hdinsight-aks-variables-tenant-id=`<Tenant from previous step> `

    ```python
    from airflow.models import Variable 

    def retrieve_variable_from_akv(): 

        variable_value = Variable.get("client-id") 

        print(variable_value) 
    ```

 
## DAG definition 

A DAG (Directed Acyclic Graph) is the core concept of Airflow, collecting Tasks together, organized with dependencies and relationships to say how they should run. 

There are three ways to declare a DAG: 

  - You can use a context manager, which adds the DAG to anything inside it implicitly 

  - You can use a standard constructor, passing the DAG into any operators you use 

  - You can use the @dag decorator to turn a function into a DAG generator (from airflow.decorators import dag) 

DAGs are nothing without Tasks to run, and those are come in the form of either Operators, Sensors or TaskFlow. 

You can read more details about DAGs, Control Flow, SubDAGs, TaskGroups, etc. directly from [Apache Airflow](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/dags.html).   

## DAG execution 

Example code is available on the [git](https://github.com/sethiaarun/hdinsight-aks/blob/spark-airflow-example/spark/Airflow/airflow-python-example-code.py); download the code locally on your computer and upload the wordcount.py to a blob storage. Follow the [steps](/azure/data-factory/how-does-workflow-orchestration-manager-work#steps-to-import) to import DAG into your workflow created during setup.

The airflow-python-example-code.py is an example of orchestrating a Spark job submission using Apache Spark with HDInsight on AKS. The example is based on [SparkPi](https://github.com/apache/spark/blob/master/examples/src/main/scala/org/apache/spark/examples/SparkPi.scala) example provided on Apache Spark.

The DAG has the following steps: 

1. get `OAuth Token` 

1. Invoke Apache Spark Livy Batch API to submit a new job 

The DAG expects to have setup for the Service Principal, as described during the setup process for the OAuth Client credential and pass the following input configuration for the execution. 

### Execution steps 

1. Execute the DAG from the [Airflow UI](https://airflow.apache.org/docs/apache-airflow/stable/ui.html), you can open the Azure Data Factory Workflow Orchestration Manager UI by clicking on Monitor icon.

    :::image type="content" source="./media/spark-job-orchestration/airflow-user-interface-step-1.png" alt-text="Screenshot shows open the Azure Data Factory Workflow Orchestration Manager UI by clicking on monitor icon." lightbox="./media/spark-job-orchestration/airflow-user-interface-step-1.png":::

1. Select the “SparkWordCountExample” DAG from the “DAGs” page.  

    :::image type="content" source="./media/spark-job-orchestration/airflow-user-interface-step-2.png" alt-text="Screenshot shows select the Spark word count example." lightbox="./media/spark-job-orchestration/airflow-user-interface-step-2.png":::

1. Click on the “execute” icon from the top right corner and select “Trigger DAG w/ config”.  

    :::image type="content" source="./media/spark-job-orchestration/airflow-user-interface-step-3.png" alt-text="Screenshot shows select execute icon." lightbox="./media/spark-job-orchestration/airflow-user-interface-step-3.png":::

            
1. Pass required configuration JSON 

    ```JSON
    { 

      "spark_cluster_fqdn":"<<domain name>>.eastus2.hdinsightaks.net", 

      "app_jar_path":"abfs://filesystem@<storageaccount>.dfs.core.windows.net", 

      "job_name":"<job_name>", 

    } 
    ```

1. Click on “Trigger” button, it starts the execution of the DAG. 

1. You can visualize the status of DAG tasks from the DAG run 

    :::image type="content" source="./media/spark-job-orchestration/dag-task-status.png" alt-text="Screenshot shows dag task status." lightbox="./media/spark-job-orchestration/dag-task-status.png":::

1. Validate the job from “Apache Spark History Server” 

    :::image type="content" source="./media/spark-job-orchestration/validate-job-execution.png" alt-text="Screenshot shows validate job execution." lightbox="./media/spark-job-orchestration/validate-job-execution.png":::

## Example code 

This is an example of orchestrating data pipeline using Airflow with HDInsight on AKS 
 
The example is based on [SparkPi](https://github.com/apache/spark/blob/master/examples/src/main/scala/org/apache/spark/examples/SparkPi.scala) example provided on Apache Spark.

### Reference

- Refer to the [sample code](https://github.com/Azure-Samples/hdinsight-aks/blob/main/spark/Airflow/airflow-python-example-code.py).
- [Apache Spark Website](https://spark.apache.org/)
- Apache, Apache Airflow, Airflow, Apache Spark, Spark, and associated open source project names are [trademarks](../trademarks.md) of the [Apache Software Foundation](https://www.apache.org/) (ASF).
