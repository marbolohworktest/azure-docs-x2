---
title: "Tutorial: Connect to a managed Admin for Spring in Azure Container Apps"
description: Learn how to use a managed Admin for Spring in Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: azure-container-apps
ms.custom: devx-track-extended-java
ms.topic: conceptual
ms.date: 07/15/2024
ms.author: cshoe
---

# Tutorial: Connect to a managed Admin for Spring in Azure Container Apps

The Admin for Spring managed component offers an administrative interface for Spring Boot web applications that expose actuator endpoints. As a managed component in Azure Container Apps, you can easily bind your container app to Admin for Spring for seamless integration and management.

This tutorial shows you how to create an Admin for Spring Java component and bind it to your container app so that you can monitor and manage your Spring applications with ease.

:::image type="content" source="media/java-components/spring-boot-admin-overview.png" alt-text="Screenshot that shows an overview of the Admin for Spring insights dashboard."  lightbox="media/java-components/spring-boot-admin-overview.png":::

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create an Admin for Spring Java component.
> * Bind your container app to an Admin for Spring Java component.

If you want to integrate Admin for Spring with Eureka Server for Spring, see [Integrate Admin for Spring with Eureka Server for Spring in Container Apps](java-admin-eureka-integration.md) instead.

> [!IMPORTANT]
> This tutorial uses services that can affect your Azure bill. If you decide to follow along, make sure you delete the resources featured in this article to avoid unexpected billing.

## Prerequisites

To finish this project, you need the following items:

| Requirement  | Instructions |
|--|--|
| [Azure account](https://azure.microsoft.com/free/) | An active subscription is required. If you don't have one, you [can create one for free](https://azure.microsoft.com/free/). |
| [Azure CLI](/cli/azure/install-azure-cli) | Install the [Azure CLI](/cli/azure/install-azure-cli).|

## Considerations

When you run the Admin for Spring component in Container Apps, be aware of the following details:

[!INCLUDE [container-apps/component-considerations.md](../../includes/container-apps/component-considerations.md)]

## Setup

Before you begin to work with the Admin for Spring component, you first need to create the required resources.

### [Azure CLI](#tab/azure-cli)

The following commands help you create your resource group and container app environment.

1. Create variables to support your application configuration. These values are provided for you for the purposes of this lesson.

    ```bash
    export LOCATION=eastus
    export RESOURCE_GROUP=my-resource-group
    export ENVIRONMENT=my-environment
    export JAVA_COMPONENT_NAME=admin
    export APP_NAME=sample-admin-client
    export IMAGE="mcr.microsoft.com/javacomponents/samples/sample-admin-for-spring-client:latest"
    ```

    | Variable | Description |
    |---|---|
    | `LOCATION` | The Azure region location where you create your container app and Java component. |
    | `ENVIRONMENT` | The container app environment name for your demo application. |
    | `RESOURCE_GROUP` | The Azure resource group name for your demo application. |
    | `JAVA_COMPONENT_NAME` | The name of the Java component created for your container app. In this case, you create an Admin for Spring Java component.  |
    | `IMAGE` | The container image used in your container app. |

1. Sign in to Azure with the Azure CLI.

    ```azurecli
    az login
    ```

1. Create a resource group.

    ```azurecli
    az group create \
      --name $RESOURCE_GROUP \
      --location $LOCATION \
      --query "properties.provisioningState"
    ```

    When you use the `--query` parameter, the response filters down to a simple success or failure message.

1. Create your container app environment.

    ```azurecli
    az containerapp env create \
      --name $ENVIRONMENT \
      --resource-group $RESOURCE_GROUP \
      --location $LOCATION
    ```

### [Azure portal](#tab/azure-portal)

To create each of the resources necessary to create a container app, follow these steps:

1. Search for **Container Apps** in the Azure portal and select **Create**.

1. On the **Basics** tab, enter the following values:

    | Property | Value |
    |---|---|
    | Subscription | Select your Azure subscription. |
    | Resource group | Select **Create new** to create a new resource group named **my-resource-group**. |
    | Container app name | Enter **sample-admin-client**.  |
    | Deployment source | Select **Container image**. |
    | Region | Select the region nearest you. |
    | Container app environment | Select **Create new** to create a new environment. |

1. On the **Create Container Apps environment** window, enter the following values:

    | Property | Value |
    |---|---|
    | Environment name | Enter **my-environment**. |
    | Zone redundancy | Select **Disabled**.  |
  
1. Select **Create**, and then select the **Container** tab.
  
1. On the **Container** tab, enter the following values:

    | Property | Value |
    |---|---|
    | Name | Enter **sample-admin-client**. |
    | Image source | Select **Docker Hub or other registries**. |
    | Image type | Select **Public**. |
    | Registry sign-in server | Enter **mcr.microsoft.com**. |
    | Image and tag | Enter **javacomponents/samples/sample-admin-for-spring-client:latest**. |
  
1. Select the **Ingress** tab.

1. On the **Ingress** tab, enter the following values and leave the rest of the form filled in with the default values.

    | Property | Value |
    |---|---|
    | Ingress | Select **Enabled**. |
    | Ingress traffic | Select **Accept traffic from anywhere**. |
    | Ingress type | Select **HTTP**. |
    | Target port | Enter **8080**. |
  
1. Select **Review + create**.

1. After the validation checks pass, select **Create** to create your container app.

---

## Use the component

### [Azure CLI](#tab/azure-cli)

Now that you have an existing environment, you can create your container app and bind it to a Java component instance of an Admin for Spring component.

1. Create the Admin for Spring Java component.

    ```azurecli
    az containerapp env java-component admin-for-spring create \
      --environment $ENVIRONMENT \
      --resource-group $RESOURCE_GROUP \
      --name $JAVA_COMPONENT_NAME \
      --min-replicas 1 \
      --max-replicas 1
    ```

1. Update the Admin for Spring Java component.

    ```azurecli
    az containerapp env java-component admin-for-spring create \
      --environment $ENVIRONMENT \
      --resource-group $RESOURCE_GROUP \
      --name $JAVA_COMPONENT_NAME \
      --min-replicas 2 \
      --max-replicas 2
    ```

### [Azure portal](#tab/azure-portal)

Now that you have an existing environment and admin client container app, you can create a Java component instance of Admin for Spring.

1. Go to your container app's environment in the portal.

1. On the service menu, under **Services**, select **Services**.

1. Select the **+ Configure** dropdown, and select **Java component**.

1. On the **Configure Java component** pane, enter the following values:

    | Property | Value |
    |---|---|
    | Java component type | Select **Admin for Spring**. |
    | Java component name | Enter **admin**. |

1. Select **Next**.

1. On the **Review** tab, select **Configure**.

---

## Bind your container app to the Admin for Spring Java component

### [Azure CLI](#tab/azure-cli)

1. Create the container app and bind it to the Admin for Spring component.

    ```azurecli
    az containerapp create \
      --name $APP_NAME \
      --resource-group $RESOURCE_GROUP \
      --environment $ENVIRONMENT \
      --image $IMAGE \
      --min-replicas 1 \
      --max-replicas 1 \
      --ingress external \
      --target-port 8080 \
      --bind $JAVA_COMPONENT_NAME
    ```

### [Azure portal](#tab/azure-portal)

1. Go to your Container App environment in the portal.

1. On the service menu, under **Services**, select **Services**.

1. From the list, select **admin**.

1. Under **Bindings**, select the **App name** dropdown, and select **sample-admin-client**.

1. Select the **Review** tab.

1. Select **Configure**.

1. Return to your container app in the portal. Copy the URL of your app to a text editor so that you can use it in an upcoming step.

---

The bind operation binds the container app to the Admin for Spring Java component. The container app can now read the configuration values from environment variables, primarily the `SPRING_BOOT_ADMIN_CLIENT_URL` property, and connect to the Admin for Spring component.

The binding also injects the following property:

```bash
"SPRING_BOOT_ADMIN_CLIENT_INSTANCE_PREFER-IP": "true",
```

This property indicates that the Admin for Spring component client should prefer the IP address of the container app instance when you connect to the Admin for Spring server.

## Optional: Unbind your container app from the Admin for Spring Java component

### [Azure CLI](#tab/azure-cli)

To remove a binding from a container app, use the `--unbind` option.

``` azurecli
  az containerapp update \
    --name $APP_NAME \
    --unbind $JAVA_COMPONENT_NAME \
    --resource-group $RESOURCE_GROUP
```

### [Azure portal](#tab/azure-portal)

1. Go to your container app environment in the portal.

1. On the service menu, under **Services**, select **Services**.

1. From the list, select **admin**.

1. Under **Bindings**, find the line for **sample-admin-client**, select it, and select **Delete**.

1. Select **Next**.

1. Select the **Review** tab.

1. Select **Configure**.

---

## View the dashboard

> [!IMPORTANT]
> To view the dashboard, you need to have at least the `Microsoft.App/managedEnvironments/write` role assigned to your account on the managed environment resource. You can explicitly assign the `Owner` or `Contributor` role on the resource. You can also follow the steps to create a custom role definition and assign it to your account.

1. Create the custom role definition.

    ```azurecli
    az role definition create --role-definition '{
        "Name": "<ROLE_NAME>",
        "IsCustom": true,
        "Description": "Can access managed Java Component dashboards in managed environments",
        "Actions": [
            "Microsoft.App/managedEnvironments/write"
        ],
        "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"]
    }'
    ```

    Make sure to replace the placeholders in between the `<>` brackets with your values.

1. Assign the custom role to your account on the managed environment resource.

    Get the resource ID of the managed environment:

    ```azurecli
    export ENVIRONMENT_ID=$(az containerapp env show \
        --name $ENVIRONMENT --resource-group $RESOURCE_GROUP \ 
        --query id -o tsv)
    ```

1. Assign the role to your account.

    Before you run this command, replace the placeholder in between the `<>` brackets with your user or service principal ID.

    ```azurecli
    az role assignment create \
      --assignee <USER_OR_SERVICE_PRINCIPAL_ID> \
      --role "<ROLE_NAME>" \
      --scope $ENVIRONMENT_ID
    ```

    > [!NOTE]
    > The <USER_OR_SERVICE_PRINCIPAL_ID> property usually should be the identity that you use to access the Azure portal. The <ROLE_NAME> property is the name that you assigned in step 1.

1. Get the URL of the Admin for Spring dashboard.

    ```azurecli
    az containerapp env java-component admin-for-spring show \
      --environment $ENVIRONMENT \
      --resource-group $RESOURCE_GROUP \
      --name $JAVA_COMPONENT_NAME \
      --query properties.ingress.fqdn -o tsv
    ```

    This command returns the URL that you can use to access the Admin for Spring dashboard. With the dashboard, you can also see your container app, as shown in the following screenshot.

    :::image type="content" source="media/java-components/spring-boot-admin-alone.png" alt-text="Screenshot that shows the overview of the Admin for Spring dashboard."  lightbox="media/java-components/spring-boot-admin-alone.png":::

## Clean up resources

The resources created in this tutorial have an effect on your Azure bill. If you aren't going to use these services long term, run the following command to remove everything you created in this tutorial.

```azurecli
az group delete \
  --resource-group $RESOURCE_GROUP
```

## Next step

> [!div class="nextstepaction"]
> [Configure Admin for Spring settings](java-admin-for-spring-usage.md)

## Related content

[Integrate the managed Admin for Spring with Eureka Server for Spring](java-admin-eureka-integration.md)
