---
title: Create an Azure Event Grid topic or a domain
description: This article shows how to create an Event Grid topic or domain.
ms.date: 01/31/2024
ms.topic: how-to
ms.custom: mode-ui
---

# Create a custom topic or a domain in Azure Event Grid
This article shows how to create a custom topic or a domain in Azure Event Grid. 

## Prerequisites
If you're new to Azure Event Grid, read through [Event Grid overview](overview.md) before starting this tutorial.

[!INCLUDE [register-provider.md](./includes/register-provider.md)]

## Create a custom topic or domain
An Event Grid topic provides a user-defined endpoint that you post your events to. 

1. Sign in to [Azure portal](https://portal.azure.com/).
2. In the search bar at the top, type **Event Grid Topics**, and then select **Event Grid Topics** from the drop-down list. To create a domain, search for **Event Grid Domains**.

    :::image type="content" source="./media/custom-event-quickstart-portal/select-topics.png" lightbox="./media/custom-event-quickstart-portal/select-topics.png" alt-text="Screenshot showing the Azure port search bar to search for Event Grid topics.":::
3. On the **Event Grid Topics** or **Event Grid Domains** page, select **+ Create** on the toolbar. 

    :::image type="content" source="./media/custom-event-quickstart-portal/create-topic-button.png" alt-text="Screenshot showing the Create Topic button on Event Grid topics page.":::

## Basics page
On the **Basics** page of **Create Topic** or **Create Event Grid Domain** wizard, follow these steps:

1. Select your Azure **subscription**.
2. Select an existing resource group or select **Create new**, and enter a **name** for the **resource group**.
3. Provide a unique **name** for the custom topic or domain. The name must be unique because it's represented by a Domain Name System (DNS) entry. Don't use the name shown in the image. Instead, create your own name - it must be between 3-50 characters and contain only values a-z, A-Z, 0-9, and "-".
4. Select a **location** for the Event Grid topic or domain.
1. Select **Next: Networking** at the bottom of the page to switch to the **Networking** page.  

    :::image type="content" source="./media/create-custom-topic/basics-page.png" alt-text="Screenshot showing the Networking page of the Create Topic wizard.":::
    
## Networking page
On the **Networking** page of the **Create Topic** or **Create Event Grid Domain** wizard, follow these steps:

1. If you want to allow clients to connect to the topic or domain endpoint via a public IP address, keep the **Public access** option selected. You can restrict the access to specific IP addresses or IP address range. 

    :::image type="content" source="./media/configure-firewall/networking-page-public-access.png" alt-text="Screenshot showing the selection of Public access option on the Networking page of the Create topic wizard.":::
1. To allow access to the topic or domain via a private endpoint, select the **Private access** option. 

    :::image type="content" source="./media/configure-firewall/networking-page-private-access.png" alt-text="Screenshot showing the selection of Private access option on the Networking page of the Create topic wizard. ":::    
1. Follow instructions in the [Add a private endpoint using Azure portal](configure-private-endpoints.md#use-azure-portal) section to create a private endpoint. 
1. Select **Next: Security** at the bottom of the page to switch to the **Security** page. 


## Security page
On the **Security** page of the **Create Topic**  or **Create Event Grid Domain** wizard, follow these steps:

1. To assign a system-assigned managed identity to your topic or domain, select **Enable system assigned identity**. 

    :::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="Screenshot of the Identity page with system assigned identity option selected."::: 
1. To assign a user-assigned identity, select **Add user assigned identity** in the **User assigned identity** section of the page. 
1. In the **Select user assigned identity** window, select the subscription that has the user-assigned identity, select the **user-assigned identity**, and then click **Select**. 

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="Screenshot of the Identity page with user assigned identity option selected." lightbox="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png":::
1. To disable local authentication, select **Disabled**. When you do it, the topic or domain can't be accessed using accesskey and SAS authentication, but only via Microsoft Entra authentication.

    :::image type="content" source="./media/authenticate-with-microsoft-entra-id/create-topic-disable-local-auth.png" alt-text="Screenshot showing the Advanced tab of Create Topic page when you can disable local authentication.":::
1. Configure the minimum required Transport Layer Security (TLS) version. For more information, see [Configure minimum TLS version](transport-layer-security-configure-minimum-version.md).

    :::image type="content" source="./media/create-custom-topic/configure-transport-layer-security-version.png" alt-text="Screenshot showing the Advanced tab of Create Topic page when you can select the minimum TLS version.":::
1. Select **Advanced** at the bottom of the page to switch to the **Advanced** page. 

## Advanced page
1. On the **Advanced** page of the **Create Topic**  or **Create Event Grid Domain** wizard, select the schema for events that will be published to this topic. 

     :::image type="content" source="./media/create-custom-topic/select-schema.png" alt-text="Screenshot showing the selection of a schema on the Advanced page.":::
2. For **Data residency**, select whether you don't want any data to be replicated to another region (**Regional**) or you want the metadata to be replicated to a predefined secondary region (**Cross-Geo**). 

    :::image type="content" source="./media/create-custom-topic/data-residency.png" alt-text="Screenshot showing the Data residency section of the Advanced page in the Create Topic wizard.":::

    The **Cross-Geo** option allows Microsoft-initiated failover to the paired region when there's a region failure. For more information, see [Server-side geo disaster recovery in Azure Event Grid](geo-disaster-recovery.md). Microsoft-initiated failover is exercised by Microsoft in rare situations to fail over Event Grid resources from an affected region to the corresponding geo-paired region. This process doesn't require an intervention from user. Microsoft reserves right to make a determination of when this path will be taken. The mechanism doesn't involve a user consent before the user's topic or domain is failed over. For more information, see [How do I recover from a failover?](./faq.yml).

    If you select the **Regional** option, you can define your own disaster recovery plan. 
3. Select **Next: Tags** to move to the **Tags** page. 

## Tags page
The **Tags** page has no fields that are specific to Event Grid. You can assign a tag (name-value pair) as you do for any other Azure resource. Select **Next: Review + create** to switch to the **Review + create** page. 

## Review + create page
On the **Review + create** page, review all your settings, confirm the validation succeeded, and then select **Create** to create the topic or the domain.

:::image type="content" source="./media/create-custom-topic/review-create.png" alt-text="Screenshot showing the Review + create page.":::


## Next steps

Now that you know how to create custom topics or domains, learn more about what Event Grid can help you do:

- [Route custom events to web endpoint with the Azure portal and Event Grid](custom-event-quickstart-portal.md)
- [About Event Grid](overview.md)
- [Event handlers](event-handlers.md)

See the following samples to learn about publishing events to and consuming events from Event Grid using different programming languages. 

- [Azure Event Grid samples for .NET](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Azure Event Grid samples for Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Azure Event Grid samples for Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [Azure Event Grid samples for JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [Azure Event Grid samples for TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
