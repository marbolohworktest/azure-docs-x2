---
title: Azure Orbital Ground Station - update spacecraft TLE
description: Update the spacecraft Two-Line Element (TLE) of an existing spacecraft resource before you schedule a contact.
author: hrshelar
ms.service: azure-orbital
ms.topic: tutorial
ms.custom: ga
ms.date: 12/06/2022
ms.author: mosagie
# Customer intent: As a satellite operator, I want to ingest data from my satellite into Azure.
---

# Update the spacecraft TLE

Update the TLE of an existing spacecraft resource.

 > [!NOTE]
   > TLE stands for Two-Line Element.
   > 
   > Be sure to update the TLE value before you schedule a contact. A TLE that's more than two weeks old might result in an unsuccessful downlink.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A registered spacecraft. Learn more on how to [register spacecraft](register-spacecraft.md).

## Azure portal method

1.	In the Azure portal search box, enter **Spacecraft**. Select **Spacecraft** in the search results.
2.	In the **Spacecraft** page, select the name of the spacecraft for which to update the ephemeris.
3.	Select **Ephemeris** on the left menu bar of the spacecraft’s overview.
4.	In Ephemeris, enter this information in each of the required fields:

    | **Field** | **Value** |
    | --- | --- |
    | TLE title line | Spacecraft updated TLE Title Line |
    | TLE Line 1 | Updated TLE Line 1 |
    | TLE Line 2 | Updated TLE Line 2 |

    :::image type="content" source="media/orbital-eos-ephemeris.png" alt-text="Spacecraft TLE update" lightbox="media/orbital-eos-ephemeris.png":::

5. Select the **Submit** button.

## API method

Use the Spacecrafts REST Operation Group to [update a spacecraft's TLE](/rest/api/orbital/azureorbitalgroundstation/spacecrafts/create-or-update/) in the Azure Orbital Ground Station API.

## Next steps

- [Schedule a contact](schedule-contact.md)
