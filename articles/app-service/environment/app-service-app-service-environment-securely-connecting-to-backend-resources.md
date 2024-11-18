---
title: Connect to back end v1
description: Learn about how to securely connect to backend resources from an App Service Environment. This doc is provided only for customers who use the legacy v1 ASE.
author: madsd

ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 03/29/2022
ms.author: madsd
---
# Connect securely to back end resources from an App Service environment

> [!IMPORTANT]
> This article is about App Service Environment v1. [App Service Environment v1 and v2 are retired as of 31 August 2024](https://aka.ms/postEOL/ASE). There's a new version of App Service Environment that is easier to use and runs on more powerful infrastructure. To learn more about the new version, start with the [Introduction to the App Service Environment](overview.md). If you're currently using App Service Environment v1, please follow the steps in [this article](upgrade-to-asev3.md) to migrate to the new version.
>
> As of 31 August 2024, [Service Level Agreement (SLA) and Service Credits](https://aka.ms/postEOL/ASE/SLA) no longer apply for App Service Environment v1 and v2 workloads that continue to be in production since they are retired products. Decommissioning of the App Service Environment v1 and v2 hardware has begun, and this may affect the availability and performance of your apps and data.
>
> You must complete migration to App Service Environment v3 immediately or your apps and resources may be deleted. We will attempt to auto-migrate any remaining App Service Environment v1 and v2 on a best-effort basis using the [in-place migration feature](migrate.md), but Microsoft makes no claim or guarantees about application availability after auto-migration. You may need to perform manual configuration to complete the migration and to optimize your App Service plan SKU choice to meet your needs. If auto-migration isn't feasible, your resources and associated app data will be deleted. We strongly urge you to act now to avoid either of these extreme scenarios.
>
> If you need additional time, we can offer a one-time 30-day grace period for you to complete your migration. For more information and to request this grace period, review the [grace period overview](./auto-migration.md#grace-period), and then go to [Azure portal](https://portal.azure.com) and visit the Migration blade for each of your App Service Environments.
>
> For the most up-to-date information on the App Service Environment v1/v2 retirement, see the [App Service Environment v1 and v2 retirement update](https://github.com/Azure/app-service-announcements/issues/469).
>

Since an App Service Environment is always created in **either** an Azure Resource Manager virtual network, **or** a classic deployment model [virtual network][virtualnetwork], outbound connections from an App Service Environment to other backend resources can flow exclusively over the virtual network. As of June 2016, ASEs can also be deployed into virtual networks that use either public address ranges or RFC1918 address spaces (private addresses).  

For example, there may be a SQL Server running on a cluster of virtual machines with port 1433 locked down.  The endpoint may be ACLd to only allow access from other resources on the same virtual network.  

As another example, sensitive endpoints may run on-premises and be connected to Azure via either [Site-to-Site][SiteToSite] or [Azure ExpressRoute][ExpressRoute] connections.  As a result, only resources in virtual networks connected to the Site-to-Site or ExpressRoute tunnels may access on-premises endpoints.

For all of these scenarios, apps running on an App Service Environment may securely connect to the various servers and resources. If outbound traffic from apps runs in an App Service Environment to private endpoints in the same virtual network or connected to the same virtual network, it'll only flow over the virtual network.  Outbound traffic to private endpoints won't flow over the public Internet.

One issue applies to outbound traffic from an App Service Environment to endpoints within a virtual network. App Service Environments can't reach endpoints of virtual machines that are located in the **same** subnet as the App Service Environment. This limitation normally shouldn't be a problem, if App Service Environments are deployed into a subnet that's reserved for use exclusively by the App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## Outbound Connectivity and DNS Requirements

For an App Service Environment to function properly, it requires outbound access to various endpoints. A full list of the external endpoints used by an ASE is in the "Required Network Connectivity" section of the [Network Configuration for ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) article.

App Service Environments require a valid DNS infrastructure configured for the virtual network.  If the DNS configuration is changed after the creation of an App Service Environment, developers can force an App Service Environment to pick up the new DNS configuration. At the top of the App Service Environment management blade in the portal, select the **Restart** icon to trigger a rolling environment reboot, which causes the environment to pick up the new DNS configuration.

It's also recommended that any custom DNS servers on the vnet be set up ahead of time before creating an App Service Environment.  If a virtual network's DNS configuration is changed during the creation of an App Service Environment, that will result in the App Service Environment creation process failing. On the other end of a VPN gateway, if there's a custom DNS server that's unreachable or unavailable, the App Service Environment creation process will also fail.

## Connecting to a SQL Server

A common SQL Server configuration has an endpoint listening on port 1433:

![SQL Server Endpoint][SqlServerEndpoint]

There are two approaches for restricting traffic to this endpoint:

* [Network Access Control Lists][NetworkAccessControlLists] (Network ACLs)
* [Network Security Groups][NetworkSecurityGroups]

## Restricting Access With a Network ACL

Port 1433 can be secured using a network access control list.  The example below adds to assignment permissions the client addresses originating from within a virtual network, and blocks access to all other clients.

![Network Access Control List Example][NetworkAccessControlListExample]

Any applications that run in App Service Environment, in the same virtual network as the SQL Server, may connect to the SQL Server instance. Use the **VNet internal** IP address for the SQL Server virtual machine.  

The example connection string below references the SQL Server using its private IP address.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Although the virtual machine has a public endpoint as well, connection attempts to use the public IP address will be rejected because of the network ACL.

## Restricting Access With a Network Security Group

An alternative approach for securing access is with a network security group.  Network security groups can be applied to individual virtual machines, or to a subnet containing virtual machines.

First, you'll need to create a network security group:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Restricting access to only VNet internal traffic is simple with a network security group.  The default rules in a network security group only allow access from other network clients in the same virtual network.

As a result, locking down access to SQL Server is simple. Just apply a network security group with its default rules to either the virtual machines running SQL Server, or the subnet containing the virtual machines.

The sample below applies a network security group to the containing subnet:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

The final result is a set of security rules that block external access, while allowing VNet internal access:

![Default Network Security Rules][DefaultNetworkSecurityRules]

## Getting started

To get started with App Service Environments, see [Introduction to App Service Environment][IntroToAppServiceEnvironment]

For details around controlling inbound traffic to your App Service Environment, see [Controlling inbound traffic to an App Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png
