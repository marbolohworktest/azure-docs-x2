---
title: Manage, update, or uninstall
description: Use the Azure CLI or Azure portal to manage your Azure IoT Operations instances, including updating and uninstalling.
author: kgremban
ms.author: kgremban
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 10/24/2024

#CustomerIntent: As an OT professional, I want to manage Azure IoT Operations instances.
---

# Manage the lifecycle of an Azure IoT Operations instance

[!INCLUDE [public-preview-note](../includes/public-preview-note.md)]

Use the Azure CLI and Azure portal to manage, uninstall, or update Azure IoT Operations instances.

## Prerequisites

* An Azure IoT Operations instance deployed to a cluster. For more information, see [Deploy Azure IoT Operations](./howto-deploy-iot-operations.md).

* Azure CLI installed on your development machine. This scenario requires Azure CLI version 2.64.0 or higher. Use `az --version` to check your version and `az upgrade` to update if necessary. For more information, see [How to install the Azure CLI](/cli/azure/install-azure-cli).

* The Azure IoT Operations extension for Azure CLI. Use the following command to add the extension or update it to the latest version:

  ```azurecli
  az extension add --upgrade --name azure-iot-ops
  ```

## Manage

After deployment, you can use the Azure CLI and Azure portal to view and manage your Azure IoT Operations instance.

### List instances

#### [Azure portal](#tab/portal)

1. In the [Azure portal](https://portal.azure.com), search for and select **Azure IoT Operations**.
1. Use the filters to view Azure IoT Operations instances based on subscription, resource group, and more.

#### [Azure CLI](#tab/cli)

Use the `az iot ops list` command to see all of the Azure IoT Operations instances in your subscription or resource group.

The basic command returns all instances in your subscription.

```azurecli
az iot ops list
```

To filter the results by resource group, add the `--resource-group` parameter.

```azurecli
az iot ops list --resource-group <RESOURCE_GROUP>
```

---

### View instance

#### [Azure portal](#tab/portal)

You can view your Azure IoT Operations instance in the Azure portal.

1. In the [Azure portal](https://portal.azure.com), go to the resource group that contains your Azure IoT Operations instance, or search for and select **Azure IoT Operations**.

1. Select the name of your Azure IoT Operations instance.

1. On the **Overview** page of your instance, the **Arc extensions** table displays the resources that were deployed to your cluster.

   :::image type="content" source="../get-started-end-to-end-sample/media/quickstart-deploy/view-instance.png" alt-text="Screenshot that shows the Azure IoT Operations instance on your Arc-enabled cluster." lightbox="../get-started-end-to-end-sample/media/quickstart-deploy/view-instance.png":::

#### [Azure CLI](#tab/cli)

Use the `az iot ops show` command to view the properties of an instance.

```azurecli
az iot ops show --name <INSTANCE_NAME> --resource-group <RESOURCE_GROUP>
```

You can also use the `az iot ops show` command to view the resources in your Azure IoT Operations deployment in the Azure CLI. Add the `--tree` flag to show a tree view of the deployment that includes the specified Azure IoT Operations instance.

```azurecli
az iot ops show --name <INSTANCE_NAME> --resource-group <RESOURCE_GROUP> --tree
```

The tree view of a deployment looks like the following example:

```bash
MyCluster
├── extensions
│   ├── akvsecretsprovider
│   ├── azure-iot-operations-ltwgs
│   └── azure-iot-operations-platform-ltwgs
└── customLocations
    └── MyCluster-cl
        ├── resourceSyncRules
        └── resources
            ├── MyCluster-ops-init-instance
            └── MyCluster-observability
```

You can run `az iot ops check` on your cluster to assess health and configurations of individual Azure IoT Operations components. By default, the command checks MQ but you can [specify the service](/cli/azure/iot/ops#az-iot-ops-check-examples) with `--ops-service` parameter.

---

### Update instance tags and description

#### [Azure portal](#tab/portal)

1. In the [Azure portal](https://portal.azure.com), go to the resource group that contains your Azure IoT Operations instance, or search for and select **Azure IoT Operations**.

1. Select the name of your Azure IoT Operations instance.

1. On the **Overview** page of your instance, select **Add tags** or **edit** to modify tags on your instance.

#### [Azure CLI](#tab/cli)

Use the `az iot ops update` command to edit the tags and description parameters of your Azure IoT Operations instance. The values provided in the `update` command replace any existing tags or description

```azurecli
az iot ops update --name <INSTANCE_NAME> --resource-group <RESOURCE_GROUP> --desc "<INSTANCE_DESCRIPTION>" --tags <TAG_NAME>=<TAG-VALUE> <TAG_NAME>=<TAG-VALUE>
```

To delete all tags on an instance, set the tags parameter to a null value. For example:

```azurecli
az iot ops update --name <INSTANCE_NAME> --resource-group --tags ""
```

---

## Uninstall

The Azure CLI and Azure portal offer different options for uninstalling Azure IoT Operations.

The Azure portal steps can delete an Azure IoT Operations instance, but can't affect the related resources in the deployment. If you want to delete the entire deployment, use the Azure CLI.

### [Azure portal](#tab/portal)

1. In the [Azure portal](https://portal.azure.com), go to the resource group that contains your Azure IoT Operations instance, or search for and select **Azure IoT Operations**.

1. Select the name of your Azure IoT Operations instance.

1. On the **Overview** page of your instance, select **Delete**.

1. Review the list of resources that are and aren't deleted as part of this operation, then type the name of your instance and select **Delete** to confirm.

   :::image type="content" source="./media/howto-deploy-iot-operations/delete-instance.png" alt-text="A screenshot that shows deleting an Azure IoT Operations instance in the Azure portal.":::

### [Azure CLI](#tab/cli)

Use the [az iot ops delete](/cli/azure/iot/ops#az-iot-ops-delete) command to delete the entire Azure IoT Operations deployment from a cluster. The `delete` command evaluates the Azure IoT Operations related resources on the cluster and presents a tree view of the resources to be deleted. The cluster should be online when you run this command.

The `delete` command streamlines the redeployment of Azure IoT Operations to the same cluster. It undoes the `create` command so that you can run `create`, `delete`, `create` again and so on without having to rerun `init`.

The `delete` command removes:

* The Azure IoT Operations instance
* Arc extensions
* Custom locations
* Resource sync rules
* Resources that you can configure in your Azure IoT Operations solution, like assets, MQTT broker, and dataflows.

```azurecli
az iot ops delete --name <INSTANCE_NAME> --resource-group <RESOURCE_GROUP>
```

To delete the instance and also remove the Azure IoT Operations dependencies (the output of `init`), add the flag `--include-deps`.

```azurecli
az iot ops delete --name <INSTANCE_NAME> --resource-group <RESOURCE_GROUP> --include-deps
```

---

## Upgrade

In public preview, Azure IoT Operations supports upgrading instances from version 0.7.x to 0.8.x. 

When a generally available release is made available, you'll need to deploy a new Azure IoT Operations installation. You won't be able to upgrade from a preview installation.

### [Azure portal](#tab/portal)

1. In the [Azure portal](https://portal.azure.com), go to the resource group that contains your Azure IoT Operations instance, or search for and select **Azure IoT Operations**.

1. Select the name of your Azure IoT Operations instance.

1. On the **Overview** page of your instance, select **Upgrade**.

1. The **Upgrade Azure IoT Operations** wizard prompts you to make sure you have the latest version for the Azure IoT Operations CLI extension. Copy and run the provided `az extension add` command.

1. Update to the latest version of Azure IoT Operations instance. Copy and run the provided `az iot ops upgrade` command.

1. Once the upgrade command completes successfully, you can exit the wizard and refresh your instance page.

### [Azure CLI](#tab/cli)

Use the `az iot ops upgrade` command to upgrade an Azure IoT Operations deployment. This command:

* Upgrades Azure Arc extensions on your cluster.
* Upgrades the Azure IoT Operations instance.

```azurecli
az iot ops upgrade --resource-group <RESOURCE_GROUP> --name <INSTANCE_NAME>
```

---