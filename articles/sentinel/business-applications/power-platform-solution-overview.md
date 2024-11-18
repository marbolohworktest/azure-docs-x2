---
title: Microsoft Sentinel solution for Microsoft Power Platform overview
description: Learn about the Microsoft Sentinel Solution for Power Platform.
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.date: 02/28/2024
#Customer intent: As a security operations manager, I want to understand how I can use Microsoft Sentinel to monitor and detect suspicious activities in my Power Platform environment so that I can protect my organization from potential threats and data breaches.

---

# Microsoft Sentinel solution for Microsoft Power Platform overview

The Microsoft Sentinel solution for Power Platform allows you to monitor and detect suspicious or malicious activities in your Power Platform environment. The solution collects activity logs from different Power Platform components and inventory data. It analyzes those activity logs to detect threats and suspicious activities like the following activities:

- Power Apps execution from unauthorized geographies
- Suspicious data destruction by Power Apps
- Mass deletion of Power Apps
- Phishing attacks made possible through Power Apps
- Power Automate flows activity by departing employees
- Microsoft Power Platform connectors added to the environment
- Update or removal of Microsoft Power Platform data loss prevention policies

> [!IMPORTANT]
> - The Microsoft Sentinel solution for Power Platform is currently in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
> - The solution is a premium offering. Pricing information will be available before the solution becomes generally available.
> - Provide feedback for this solution by completing this survey: [https://aka.ms/SentinelPowerPlatformSolutionSurvey](https://aka.ms/SentinelPowerPlatformSolutionSurvey).

## Why you should install the solution

 The Microsoft Sentinel solution for Microsoft Power Platform helps organizations to:

- Collect Microsoft Power Platform and Power Apps activity logs, audits, and events into the Microsoft Sentinel workspace.
- Detect execution of suspicious, malicious, or illegitimate activities within Microsoft Power Platform and Power Apps.
- Investigate threats detected in Microsoft Power Platform and Power Apps and contextualize them with other user activities across the organization.
- Respond to Microsoft Power Platform-related and Power Apps-related threats and incidents in a simple and canned manner manually, automatically, or through a predefined workflow.

## Solution updates

Starting on October 17, 2024, audit logging data for Power Apps, Power Platform DLP, and Power Platform Connectors is routed to the `PowerPlatformAdminActivity` table instead of the `PowerAppsActivity`, `PowerPlatformDlpActivity` and `PowerPlatformConnectorActivity` tables.

Security content in the Microsoft Sentinel solution for Microsoft Power Platform is updated with the new table and schemas for the Power Apps, Power Platform DLP, and Power Platform Connectors. We recommend that you update the Power Platform solution in your workspace to the latest version and apply the updated analytics rule templates to benefit from the changes. For more information, see [Install or update content](../sentinel-solutions-deploy.md#install-or-update-content).

Customers using deprecated data connectors for Power Apps, Power Platform DLP, and Power Platform Connectors can safely disconnect and remove these connectors from their Microsoft Sentinel workspace. All associated data flows are ingested using Power Platform Admin Activity connector.

For more information, see [Message center](https://portal.office.com/adminportal/home?#/MessageCenter/:/messages/MC912045).

## What the solution includes

The Microsoft Sentinel solution for Power Platform includes several data connectors and analytic rules.

### Data connectors

The Microsoft Sentinel solution for Power Platform ingests and cross-correlates activity logs and inventory data from multiple sources. So, the solution requires that you enable the following data connectors that are available as part of the solution.

|Connector name  |Data collected  |Log Analytics tables |
|---------|---------|---------|
|Power Platform Inventory (using Azure Functions)   |  Power Apps and Power Automate inventory data <br><br> For more information, see [Set up Microsoft Power Platform self-service analytics to export Power Platform inventory and usage data](/power-platform/admin/self-service-analytics).      |   PowerApps_CL,<br>PowerPlatrformEnvironments_CL,<br>PowerAutomateFlows_CL,<br>PowerAppsConnections_CL      |
|Microsoft Power Platform Admin Activity (Preview)|Power Platform administrator activity logs<br><Br> For more information, see [View Power Platform administrative logs using auditing solutions in Microsoft Purview (preview)](/power-platform/admin/admin-activity-logging).|PowerPlatformAdminActivity|
|Microsoft Dataverse (Preview) |    Dataverse and model-driven apps activity logging <br><br>For more information, see [Microsoft Dataverse and model-driven apps activity logging](/power-platform/admin/enable-use-comprehensive-auditing).<br><br>If you use the data connector for Dynamics 365, migrate to the data connector for Microsoft Dataverse. This data connector replaces the legacy data connector for Dynamics 365 and supports data collection rules.  |   DataverseActivity      |

### Analytic rules

The solution includes analytics rules to detect threats and suspicious activity in your Power Platform environment. These activities include Power Apps being run from unauthorized geographies, suspicious data destruction by Power Apps, mass deletion of Power Apps, and more. For more information, see [Microsoft Sentinel solution for Microsoft Power Platform: security content reference](power-platform-solution-security-content.md).

## Parsers

The solution includes parsers that are used to access data from the raw data tables. Parsers ensure that the correct data is returned with a consistent schema. We recommend that you use the parsers instead of directly querying the inventory tables and watchlists. For more information, see [Microsoft Sentinel solution for Microsoft Power Platform: security content reference](power-platform-solution-security-content.md).

## Next steps

[Deploy the Microsoft Sentinel solution for Microsoft Power Platform](deploy-power-platform-solution.md)
