---
title: Migration guidance from Change Tracking and inventory using Log Analytics to Azure Monitoring Agent
description: An overview on how to migrate from Change Tracking and inventory using Log Analytics to Azure Monitoring Agent.
author: snehasudhirG
services: automation
ms.subservice: change-inventory-management
ms.topic: how-to
ms.date: 10/10/2024
ms.author: sudhirsneha
ms.custom:
ms.service: azure-automation
---

# Migration guidance from Change Tracking and inventory using Log Analytics to Change Tracking and inventory using Azure Monitoring Agent version

**Applies to:** :heavy_check_mark: Windows VMs :heavy_check_mark: Linux VMs :heavy_check_mark: Azure Arc-enabled servers.

This article provides guidance to move from Change Tracking and Inventory using Log Analytics (LA) version to the Azure Monitoring Agent (AMA) version. 

Using the Azure portal, you can migrate from Change Tracking & Inventory with LA agent to Change Tracking & Inventory with AMA and there are two ways to do this migration: 

- Migrate single/multiple VMs from the Virtual Machines page.
- Migrate multiples VMs on LA version solution within a particular Automation Account.

> [!NOTE]
> File Integrity Monitoring (FIM) using [Microsoft Defender for Endpoint (MDE)](https://learn.microsoft.com/azure/defender-for-cloud/file-integrity-monitoring-enable-defender-endpoint) is now currently available. Follow the guidance to migrate from:
> - [FIM with Change Tracking and Inventory using AMA](https://learn.microsoft.com/azure/defender-for-cloud/migrate-file-integrity-monitoring#migrate-from-fim-over-ama).
> - [FIM with Change Tracking and Inventory using MMA](https://learn.microsoft.com/azure/defender-for-cloud/migrate-file-integrity-monitoring#migrate-from-fim-over-mma).

## Onboarding to Change tracking and inventory using Azure Monitoring Agent

### [Single Azure VM - Azure portal](#tab/ct-single-vm)

To onboard through Azure portal, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com) and select your virtual machine
1. Under **Operations** ,  select **Change tracking**.
1. Select **Configure with AMA** and in the **Configure with Azure monitor agent**,  provide the **Log analytics workspace** and select **Migrate** to initiate the deployment.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/onboarding-single-vm-inline.png" alt-text="Screenshot of onboarding a single VM to Change tracking and inventory using Azure monitoring agent." lightbox="media/guidance-migration-log-analytics-monitoring-agent/onboarding-single-vm-expanded.png":::

1. Select **Switch to CT&I with AMA** to evaluate the incoming events and logs across LA agent and AMA version.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/switch-versions-inline.png" alt-text="Screenshot that shows switching between log analytics and Azure Monitoring Agent after a successful migration." lightbox="media/guidance-migration-log-analytics-monitoring-agent/switch-versions-expanded.png":::

### [Automation account - Azure portal](#tab/ct-at-scale)

1. Sign in to [Azure portal](https://portal.azure.com) and select your Automation account.
1. Under **Configuration Management**, select **Change tracking** and then select **Configure with AMA**.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/onboarding-at-scale-inline.png" alt-text="Screenshot of onboarding at scale to Change tracking and inventory using Azure monitoring agent." lightbox="media/guidance-migration-log-analytics-monitoring-agent/onboarding-at-scale-expanded.png":::

1. On the **Onboarding to Change Tracking with Azure Monitoring** page, you can view your automation account and list of both Azure and Azure Arc machines that are currently on Log Analytics and ready to be onboarded to Azure Monitoring Agent of Change Tracking and inventory.
   
   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/onboarding-from-log-analytics-inline.png" alt-text="Screenshot of onboarding multiple virtual machines to Change tracking and inventory from log analytics to Azure monitoring agent." lightbox="media/guidance-migration-log-analytics-monitoring-agent/onboarding-from-log-analytics-expanded.png":::

1. On the **Assess virtual machines** tab, select the machines and then select **Next**.
1. On **Assign workspace** tab, assign a new [Log Analytics workspace resource ID](#obtain-log-analytics-workspace-resource-id) to which the settings of AMA based solution should be stored and select **Next**.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/assign-workspace-inline.png" alt-text="Screenshot of assigning new Log Analytics resource ID." lightbox="media/guidance-migration-log-analytics-monitoring-agent/assign-workspace-expanded.png":::

1. On **Review** tab, you can review the machines that are being onboarded and the new workspace.
1. Select  **Migrate** to initiate the deployment.

1. After a successful migration, select **Switch to CT&I with AMA** to compare both the LA and AMA experience.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/switch-versions-inline.png" alt-text="Screenshot that shows switching between log analytics and Azure Monitoring Agent after a successful migration." lightbox="media/guidance-migration-log-analytics-monitoring-agent/switch-versions-expanded.png":::

### [Single Azure Arc VM - Azure portal](#tab/ct-single-arcvm)

1. Sign in to the [Azure portal](https://portal.azure.com). Search for and select **Machines-Azure Arc**.

   :::image type="content" source="media/enable-vms-monitoring-agent/select-arc-machines-portal.png" alt-text="Screenshot showing how to select Azure Arc machines from the portal." lightbox="media/enable-vms-monitoring-agent/select-arc-machines-portal.png":::

1. Select the specific Arc machine with Change Tracking V1 enabled that needs to be migrated to Change Tracking V2.

1. Select **Migrate to Change Tracking with AMA** and in the **Configure with Azure monitor agent**,  provide the resource id in the **Log analytics workspace** and select **Migrate** to initiate the deployment.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/onboarding-single-arc-vm.png" alt-text="Screenshot of onboarding a single Arc VM to Change tracking and inventory using Azure monitoring agent." lightbox="media/guidance-migration-log-analytics-monitoring-agent/onboarding-single-arc-vm.png":::

1. Select **Manage Activity log connection** to evaluate the incoming events and logs across LA agent and AMA version.

### [Arc-enabled VMs - PowerShell script](#tab/ps-policy)

To onboard Arc-enabled VMs, follow the steps:

#### Prerequisites

- Ensure you have PowerShell installed. The latest version of PowerShell 7 or higher is recommended. Follow the steps to [Install PowerShell on Windows, Linux, and macOS](/powershell/scripting/install/installing-powershell).
- Obtain Read access for the specified workspace resources.
- [Install the latest version of the Az PowerShell module](/powershell/azure/install-azure-powershell). The **Az.Accounts** and **Az.OperationalInsights** modules are required to pull workspace agent configuration information.
- Ensure you have Azure credentials to run `Connect-AzAccount` and `Select-AzContext` which set the script's context.
Follow these steps to migrate using scripts.

#### Migration guidance

1. Install the [script](https://github.com/mayguptMSFT/AzureMonitorCommunity/blob/master/Azure%20Services/Azure%20Monitor/Agents/Migration%20Tools/DCR%20Config%20Generator/CTDcrGenerator/CTWorkSpaceSettingstoDCR.ps1) and run it to conduct migrations. The script does the following:

    1. It ensures the new workspace resource ID is different from the one associated with the Change Tracking and Inventory using the LA version.

    1. It migrates the settings for the following data types:
       - Windows Services
       - Linux Files
       - Windows Files
       - Windows Registry
       - Linux Daemons
      
    1. The script consists of the following **Parameters**  that require an input from you. 

      **Parameter** | **Required** | **Description** |
      --- | --- | --- |
      `InputWorkspaceResourceId`| Yes | Resource ID of the workspace associated with Change Tracking & Inventory with Log Analytics. |
      `OutputWorkspaceResourceId`| Yes | Resource ID of the workspace associated with Change Tracking & Inventory with Azure Monitoring Agent. |
      `OutputDCRName`| Yes | Custom name of the new DCR created. |
      `OutputDCRLocation`| Yes | Azure location of the output workspace ID. |
      `OutputDCRTemplateFolderPath`| Yes | Folder path where DCR templates are created. |

1. A DCR template is generated when you run the above script and the template is available in `OutputDCRTemplateFolderPath`. You have to associate the new DCR to transfer the settings to the Change Tracking and Inventory using AMA.

   1. Sign in to [Azure portal](https://portal.azure.com) and go to **Monitor** and under **Settings**, select **Data Collection Rules**.
   1. Select the data collection rule that you have created in Step 1 from the listing page.
   1. In the data collection rule page, under **Configurations**, select **Resources** and then select **Add**.
   1. In the Select a scope, from Resource types, select Machines-Azure Arc that is connected to the subscription and then select Apply to associate the ctdcr created in Step 1 to the Arc-enabled machine and it will also install the Azure Monitoring Agent extension. For more information, see [Enable Change Tracking and Inventory - for Arc-enabled VMs - using portal/CLI](enable-vms-monitoring-agent.md#enable-change-tracking-and-inventory).
 
   Install the Change Tracking extension as per the OS type for the Arc-enabled VM.
    
   **Linux**
       
   ```azurecli
   az connectedmachine extension create  --name ChangeTracking-Linux  --publisher Microsoft.Azure.ChangeTrackingAndInventory --type-handler-version 2.20  --type ChangeTracking-Linux  --machine-name XYZ --resource-group XYZ-RG  --location X --enable-auto-upgrade
   ```

   **Windows**

   ```azurecli
   az connectedmachine extension create  --name ChangeTracking-Windows  --publisher Microsoft.Azure.ChangeTrackingAndInventory --type-handler-version 2.20  --type ChangeTracking-Windows  --machine-name XYZ --resource-group XYZ-RG  --location X --enable-auto-upgrade
   ```   

   If the CT logs table schema does not exist, the script mentioned in Step 1 will fail. To troubleshoot, run the following script - 

    ```azurepowershell-interactive

    $psWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $laws
 	 # Enabling CT solution on LA ws
	 New-AzMonitorLogAnalyticsSolution -Type ChangeTracking -ResourceGroupName $resourceGroup -Location $psWorkspace.Location -WorkspaceResourceId $psWorkspace.ResourceId
    ```
---


### Compare data across Log analytics Agent and Azure Monitoring Agent version

After you complete the onboarding to Change tracking with AMA version, select **Switch to CT with AMA** on the landing page to switch across the two versions and compare the following events.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/data-compare-log-analytics-monitoring-agent-inline.png" alt-text="Screenshot of data comparison from log analytics to Azure monitoring agent." lightbox="media/guidance-migration-log-analytics-monitoring-agent/data-compare-log-analytics-monitoring-agent-expanded.png":::

For example, if the onboarding to AMA version of service takes place after 3rd November at 6:00 a.m. You can compare the data by keeping consistent filters across parameters like **Change Types**, **Time Range**. You can compare incoming logs in **Changes** section and in the graphical section to be assured on data consistency.

> [!NOTE]
> You must compare for the incoming data and logs after the onboarding to AMA version is done.

### Obtain Log Analytics Workspace Resource ID

To obtain the Log Analytics Workspace resource ID, follow these steps:

1. Sign in to [Azure portal](https://portal.azure.com)
1. In **Log Analytics Workspace**, select the specific workspace and select **Json View**.
1. Copy the **Resource ID**.

   :::image type="content" source="media/guidance-migration-log-analytics-monitoring-agent/workspace-resource-inline.png" alt-text="Screenshot that shows the log analytics workspace ID." lightbox="media/guidance-migration-log-analytics-monitoring-agent/workspace-resource-expanded.png":::


## Limitations

### [Using Azure portal](#tab/limit-single-vm)

**For single VM and Automation Account**

1. 100 VMs per Automation Account can be migrated in one instance.
1. Any VM with > 100 file/registry settings for migration via portal isn't supported now.
1. Arc VM migration isn't supported with portal, we recommend that you use PowerShell script migration.
1. For File Content changes-based settings, you have to migrate manually from LA version to AMA version of Change Tracking & Inventory. Follow the guidance listed in [Track file contents](manage-change-tracking-monitoring-agent.md#configure-file-content-changes).
1. Alerts that you configure using the Log Analytics Workspace must be [manually configured](configure-alerts.md).

### [Using PowerShell script](#tab/limit-policy)

1. For File Content changes-based settings, you must migrate manually from LA version to AMA version of Change Tracking & Inventory. Follow the guidance listed in [Track file contents](manage-change-tracking.md#track-file-contents).
1. Any VM with > 100 file/registry settings for migration via Azure portal isn't supported.
1. Alerts that you configure using the Log Analytics Workspace must be [manually configured](configure-alerts.md).

---

## Disable Change tracking using Log Analytics Agent

After you enable management of your virtual machines using Change Tracking and Inventory using Azure Monitoring Agent, you might decide to stop using Change Tracking & Inventory with LA agent version and remove the configuration from the account.

The disable method incorporates the following:
- [Removes change tracking with LA agent for selected few VMs within Log Analytics Workspace](remove-vms-from-change-tracking.md).
- [Removes change tracking with LA agent from the entire Log Analytics Workspace](remove-feature.md).

## Next steps
-  To enable from the Azure portal, see [Enable Change Tracking and Inventory from the Azure portal](../change-tracking/enable-vms-monitoring-agent.md).
