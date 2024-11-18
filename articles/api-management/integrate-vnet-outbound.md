---
title: Connect API Management instance to a private network | Microsoft Docs
description: Learn how to integrate an Azure API Management instance in the Standard v2 tier with a virtual network to access backend APIs hosted within the network.
author: dlepow
ms.author: danlep
ms.service: azure-api-management
ms.topic: how-to 
ms.date: 05/20/2024
---

# Integrate an Azure API Management instance with a private VNet for outbound connections

[!INCLUDE [api-management-availability-standard-v2](../../includes/api-management-availability-standard-v2.md)] 

This article guides you through the process of configuring *VNet integration* for your Azure API Management instance so that your API Management instance can make outbound requests to API backends that are isolated in the network.

When an API Management instance is integrated with a virtual network for outbound requests, the API Management itself is not deployed in a VNet; the gateway and other endpoints remain publicly accessible. In this configuration, the API Management instance can reach both public and network-isolated backend services.

:::image type="content" source="./media/integrate-vnet-outbound/vnet-integration.svg" alt-text="Diagram of integrating API Management instance with a delegated subnet."  :::

## Prerequisites

- An Azure API Management instance in the [Standard v2](v2-service-tiers-overview.md) pricing tier
- A virtual network with a subnet where your API Management backend APIs are hosted
   - The network must be deployed in the same region and subscription as your API Management instance.
   - The subnet should be dedicated to VNet integration. 
   - A minimum subnet size of `/26` or `/27` is recommended when creating a new subnet; `/28` can be used with an existing subnet. 
- (Optional) For testing, a sample backend API hosted within a different subnet in the virtual network. For example, see [Tutorial: Establish Azure Functions private site access](../azure-functions/functions-create-private-site-access.md).

### Permissions

You must have at least the following role-based access control permissions on the subnet or at a higher level to configure virtual network integration:

| Action | Description |
|-|-|
| Microsoft.Network/virtualNetworks/read | Read the virtual network definition |
| Microsoft.Network/virtualNetworks/subnets/read | Read a virtual network subnet definition |
| Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network |

### Register Microsoft.Web resource provider

Ensure that the subscription with the virtual network is registered for the `Microsoft.Web` resource provider. You can explicitly register the provider [by following this documentation](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

## Delegate the subnet

The subnet used for integration must be delegated to the **Microsoft.Web/serverFarms** service. The subnet can't be delegated to another service. In the subnet settings, in **Delegate subnet to a service**, select **Microsoft.Web/serverFarms**.

:::image type="content" source="media/integrate-vnet-outbound/delegate-subnet.png" alt-text="Screenshot of delegating the subnet to a service in the portal.":::

## Enable VNet integration

This section will guide you through the process of enabling VNet integration for your Azure API Management instance.

1. In the [Azure portal](https://portal.azure.com), navigate to your API Management instance.
1. In the left menu, under **Deployment + Infrastructure**, select **Network**.
1. On the **Outbound traffic** card, select **VNET integration**.

    :::image type="content" source="media/integrate-vnet-outbound/integrate-vnet.png" lightbox="media/integrate-vnet-outbound/integrate-vnet.png" alt-text="Screenshot of VNet integration in the portal.":::

1. In the **Virtual network** blade, enable the **Virtual network** checkbox.
1. Select the location of your API Management instance.
1. In **Virtual network**, select the virtual network and the delegated subnet that you want to integrate. 
1. Select **Apply**, and then select **Save**. The VNet is integrated.

    :::image type="content" source="media/integrate-vnet-outbound/vnet-settings.png" lightbox="media/integrate-vnet-outbound/vnet-settings.png" alt-text="Screenshot of VNet settings in the portal.":::

## (Optional) Test VNet integration

If you have an API hosted in the virtual network, you can import it to your Management instance and test the VNet integration. For basic steps, see [Import and publish an API](import-and-publish.md).


## Related content

* [Use a virtual network with Azure API Management](virtual-network-concepts.md)




