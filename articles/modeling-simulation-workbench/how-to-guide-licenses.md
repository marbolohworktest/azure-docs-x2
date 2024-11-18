---
title: "Manage license services: Azure Modeling and Simulation Workbench"
description: Learn how to upload a license file to chamber license service in Azure Modeling and Simulation Workbench.
author: lynnar
ms.author: lynnar
ms.reviewer: yochu
ms.service: azure-modeling-simulation-workbench
ms.topic: how-to
ms.date: 09/15/2023
# Customer intent: As a Chamber Admin in Azure Modeling and Simulation Workbench, I want to activate a license service in a chamber so that Chamber Users can run applications that require licenses.
---

# Manage a license service for Azure Modeling and Simulation Workbench

A license service automates the installation of a license manager to help customers accelerate their engineering design. License management is integrated into Azure Modeling and Simulation Workbench flows via FLEXlm, which is the most commonly used license manager.

This article shows you how to upload a license file and activate a license service for an Azure Modeling and Simulation Workbench chamber.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [prerequisite-chamber](includes/prerequisite-chamber.md)]

- A FLEXlm license file for a software vendor that requires a license. You need to buy a production environment license from a vendor such as Synopsys, Cadence, Siemens, or Ansys.

[!INCLUDE [prerequisite-user-chamber-admin](includes/prerequisite-user-chamber-admin.md)]

## Upload or update a license for FLEXlm-based tools

This section lists the steps to upload a license for a FLEXlm-based tool. First, you get the FLEXlm host ID or the virtual machine (VM) universally unique ID (UUID) from the chamber. Then you provide that value to the license vendor to get the license file. After you get the license file from the vendor, you upload it to the chamber and activate it.

1. Open your web browser and go to the [Azure portal](https://portal.azure.com/). Enter your credentials to sign in to the portal.
1. Search for **Modeling and Simulation Workbench**. Select the workbench that you want to update the licenses in from the resource list.
1. On the left menu, select **Settings** > **Chamber**. A resource list appears. Select the chamber that you want to upload the data to.
1. In the **Settings** section, select the **License** pane.
1. On the **License Overview** page, copy the **FLEXlm host ID** or **VM UUID** value. Provide this value to your license vendor to get a license file.
1. After the vendor sends you the license file, select **Update** on the **License Overview** page. The **Update license** window appears.
1. Select the chamber license service for the license file that you're uploading. Select **Enable** to enable the service. Then upload the license file from your storage space.
1. In the **Update license** pop-up dialog, select the **Update** button to activate your license service. The new license is loaded, causing the service to restart.

> [!IMPORTANT]
> Loading a new license causes the license server to restart. This could affect actively running jobs.
>
> For Siemens license files validate that:
>
> - A `saltd` compatible file is requested; and
> - The clause `VENDOR saltd` is included in the license file.

## Next steps

> [!div class="nextstepaction"]
> [Import data](./how-to-guide-upload-data.md)
