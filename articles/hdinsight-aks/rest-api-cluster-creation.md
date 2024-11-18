---
title: Manage HDInsight on AKS clusters using Azure REST API 
description: Manage HDInsight on AKS clusters using Azure REST API 
ms.service: azure-hdinsight-on-aks
ms.topic: how-to
ms.date: 09/20/2024
ROBOTS: NOINDEX
---

# Manage HDInsight on AKS clusters using Azure REST API 

[!INCLUDE [retirement-notice](includes/retirement-notice.md)]
[!INCLUDE [feature-in-preview](includes/feature-in-preview.md)]



Learn how to create an HDInsight cluster using an Azure Resource Manager template and the Azure REST API. 

The Azure REST API allows you to perform management operations on services hosted in the Azure platform, including the creation of new resources such as HDInsight clusters. 

## Create a template 

Azure Resource Manager templates are JSON documents that describe a resource group and all resources in it (such as HDInsight on AKS). This template-based approach allows you to define the resources that you need for HDInsight in one template. 
 

## Create a cluster 

Here we are going to create a cluster in an existing cluster pool. 

Variables required in the script 
- Cluster Name 
- Cluster Pool Name 
- Subscription ID 
- Resource Group Name 
- Region Name 
- Cluster Type 
- SKU 
- Worker Node count 
- MSI resource ID:
  ```
  /subscriptions/<subscription ID>/resourcegroups/<resource group name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Managed identity name>
  ```
- MSI client ID
- MSI object ID
  
  :::image type="content" source="./media/rest-api-cluster-creation/rest-api.png" alt-text="Screenshot shows overview." lightbox="./media/rest-api-cluster-creation/rest-api.png":::
  
- [Microsoft Entra user ID](/partner-center/find-ids-and-domain-names)
- [HDInsight on AKS VM list](/azure/hdinsight-aks/virtual-machine-recommendation-capacity-planning)
- recommendation-capacity-planning 

To create a cluster, copy the following command to your REST API tool. 

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusterpools/{clusterPoolName}/clusters/{clusterName}?api-version=2023-06-01-preview 
```
 
Copy the following template into your REST API body.

```
{ 

  "location": "East US", 

  "properties": { 

    "clusterType": "spark", 

    "computeProfile": { 

      "nodes": [ 

        { 

          "type": "worker", 

          "vmSize": "Standard_E8ads_v5", 

          "count": 3 

        } 

      ] 

    }, 

    "clusterProfile": { 

      "clusterVersion": "1.0.6", 

      "ossVersion": "3.3.1", 

      "identityProfile": { 

        "msiResourceId": "/subscriptions/{subscription id}/resourceGroups/{resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{Managed Identity}", 

        "msiClientId": "{}", 

        "msiObjectId": "{}" 

      }, 

      "authorizationProfile": { 

        "userIds": [ 

          "{Microsoft Entra user id}" 

        ] 

      }, 

      "serviceConfigsProfiles": [], 

      "sparkProfile": { 

        "defaultStorageUrl": "{abfs://<container name>@<storage name>.dfs.core.windows.net/}" 

      }, 

      "sshProfile": { 

        "count": 1 

      } 

    } 

  } 

} 

``` 
Run the API call.

## Next steps
To Customize and manage the cluster, please refer to the following documentation: 
[Cluster Management using REST API](/rest/api/hdinsightonaks/operation-groups)

 


 
