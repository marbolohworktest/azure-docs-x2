---
title: Configure IP firewall for Azure Event Grid namespaces
description: This article describes how to configure firewall settings for Azure Event Grid namespaces.
ms.topic: how-to
ms.custom:
  - build-2024
ms.date: 11/29/2023
author: robece
ms.author: robece
---

# Configure IP firewall for Azure Event Grid namespaces
By default, Event Grid namespaces and entities are accessible from internet as long as the request comes with valid authentication (access key) and authorization. With IP firewall, you can restrict it further to only a set of IPv4 addresses or IPv4 address ranges in [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) notation. Only the clients that fall into the allowed IP range can connect to Azure Event Grid to publish events or pull events. Clients originating from any other IP address are rejected and receive a 403 (Forbidden) response. For more information about network security features supported by Event Grid, see [Network security for Event Grid](network-security.md).

This article describes how to configure IP firewall settings for an Event Grid namespace. For complete steps for creating a namespace, see [Create and manage namespaces](create-view-manage-namespaces.md).

## Create a namespace with IP firewall settings

1. On the **Networking** page, if you want to allow clients to connect to the namespace endpoint via a public IP address, select **Public access** for **Connectivity method** if it's not already selected. 
2. You can restrict access to the topic from specific IP addresses by specifying values for the **Address range** field. Specify a single IPv4 address or a range of IP addresses in Classless inter-domain routing (CIDR) notation. 

    :::image type="content" source="./media/configure-firewall-namespaces/ip-firewall-settings.png" alt-text="Screenshot that shows IP firewall settings on the Networking page of the Create namespace wizard.":::

## Update a namespace with IP firewall settings

1. Sign-in to the [Azure portal](https://portal.azure.com).
1. In the **search box**, enter **Event Grid Namespaces** and select **Event Grid Namespaces** from the results.

    :::image type="content" source="./media/create-view-manage-namespaces/portal-search-box-namespaces.png" alt-text="Screenshot showing Event Grid Namespaces in the search results.":::
1. Select your Event Grid namespace in the list to open the **Event Grid Namespace** page for your namespace.
1. On the **Event Grid Namespace** page, select **Networking** on the left menu. 
1. Specify values for the **Address range** field. Specify a single IPv4 address or a range of IP addresses in Classless inter-domain routing (CIDR) notation. 

    :::image type="content" source="./media/configure-firewall-namespaces/namespace-ip-firewall-settings.png" alt-text="Screenshot that shows IP firewall settings on the Networking page of an existing namespace.":::

## Next steps
See [Allow access via private endpoints](configure-private-endpoints-pull.md).
