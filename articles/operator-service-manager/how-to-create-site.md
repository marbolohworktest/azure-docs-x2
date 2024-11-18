---
title: How to create a site in Azure Operator Service Manager
description: Learn how to create a site in Azure Operator Service Manager.
author: sherrygonz
ms.author: sherryg
ms.date: 09/11/2023
ms.topic: how-to
ms.service: azure-operator-service-manager
---

# Create a site in Azure Operator Service Manager

In this how-to guide, you learn how to create a site. A *site* refers to a specific location, which can be either a single Azure region (a data center location within the Azure cloud) or an on-premises facility, associated with the instantiation and management of network services.

## Prerequisites

- Ensure you can sign in to the Azure portal using an account with access to the active subscription.
- It's important to receive guidance from the designer regarding the specific NFVIs (Network Function Virtualization Infrastructure) to incorporate into your site.

## Create site

1. Sign into the Azure portal.
1. Search for **Sites** then select **Create**.
1. On the **Basics** tab, enter the *Subscription*, *Resource group*, *Name* and *Region*. You can accept the default values for the remaining settings.

    :::image type="content" source="media/how-to-create-site-basics-tab.png" alt-text="Screenshot of the Basics tab showing mandatory fields Subscription, Resource group, Name and Region." lightbox="media/how-to-create-site-basics-tab.png":::

> [!NOTE]
> The site must be in the same region as the prerequisite resources.

## Add Network Function Virtualization Infrastructure (NFVI)

Use the information in the table to add the Network Function Virtualization Infrastructure (NFVIs).

|Setting|Value|
|---|---|
| NFVI Name| Enter the name specified by the designer in NSDV| 
| NFVI Type| *Azure Core*, *Azure Operator Distributed Services* or *Unknown*.  This NFVI type value must match the NFVI type specified by the designer in the NSDV.|
| NFVI Location | The Azure region for the site.|


1. Add the Network Function Virtualization Infrastructure (NFVIs) you wish to deploy your network service on by selecting the **Add the NFVIs** tab, then *Add NFVI* once field information is input.

    :::image type="content" source="media/how-to-create-site-add-network-function-virtual-infrastructure.png" alt-text="Screenshot showing Add the NFVIs tab and fields NFVI name, NFVI type and NFVI location." lightbox="media/how-to-create-site-add-network-function-virtual-infrastructure.png":::

    > [!NOTE]
    > Consult the documentation from your NSD Designer or directly contact them to obtain the list of NFVIs.

1. Select **Review + create** and then **Create**.

## Next steps

- [Create site network service in Azure Operator Service Manager](how-to-create-site-network-service.md)