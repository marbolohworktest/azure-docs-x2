---
title: Deploy SAP S/4HANA or BW/4HANA on an Azure VM | Microsoft Docs
description: Deploy SAP S/4HANA or BW/4HANA on an Azure VM
services: virtual-machines-linux
author: pepeters
manager: timlt
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: sap-on-azure
ms.subservice: sap-vm-workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 02/15/2023
ms.author: hobruche
---
# SAP Cloud Appliance Library

[SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) offers a quick and easy way to create SAP workloads in Azure. You can set up a fully configured demo environment from an Appliance Template or deploy a standardized system for an SAP product based on default or custom SAP software installation stacks. 
This page lists the latest Appliance Templates and below the latest SAP S/4HANA stacks for production-ready deployments. 

To deploy an appliance template, you'll need to authenticate with your S-User or P-User. You can create a P-User free of charge via the [SAP Community](https://community.sap.com/). 



[For details on Azure account creation, see the SAP learning video and description](https://www.youtube.com/watch?v=iORePziUMBk&list=PLWV533hWWvDmww3OX9YPhjjS1l1n6o-H2&index=18)

You will also find detailed answers to your questions related to SAP Cloud Appliance Library on Azure [SAP CAL FAQ](https://caldocs.hana.ondemand.com/caldocs/help/Azure_FAQs.pdf)

The online library is continuously updated with Appliances for demo, proof of concept and exploration of new business cases. For the most recent ones, select “Create Appliance” here from the list – or visit [cal.sap.com](https://cal.sap.com/catalog#/applianceTemplates) for further templates.

## Deployment of appliances through SAP Cloud Appliance Library

| Appliance Template | Date | Description | Creation Link |
| ------------------ | ---- | ----------- | ------------- |
| [**SAP S/4HANA 2023 FPS01, Fully-Activated Appliance**]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/applianceTemplates/)|  May 01 2024|This appliance contains SAP S/4HANA 2023 (FPS01) with pre-activated SAP Best Practices for SAP S/4HANA core functions, and further scenarios for Service, Master Data Governance (MDG), Portfolio & Project Mgmt. (PPM), Human Capital Management (HCM), Analytics, and more. User access happens via SAP Fiori, SAP GUI, SAP HANA Studio, Windows remote desktop, or the backend operating system for full administrative access. |   [Create Appliance](https://cal.sap.com/registration?sguid=a28eacc8-388b-4c44-8172-8ff9bd436259&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| [**SAP S/4HANA 2023, Fully-Activated Appliance**]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/applianceTemplates/6ad2fc04-407f-47f8-9a1d-c94df8549ea4)| December 14 2023 | This appliance contains SAP S/4HANA 2023 (SP00) with pre-activated SAP Best Practices for SAP S/4HANA core functions, and further scenarios for Service, Master Data Governance (MDG), Portfolio Mgmt. (PPM), Human Capital Management (HCM), Analytics, and more. User access happens via SAP Fiori, SAP GUI, SAP HANA Studio, Windows remote desktop, or the backend operating system for full administrative access. | [Create Appliance](https://cal.sap.com/registration?sguid=6ad2fc04-407f-47f8-9a1d-c94df8549ea4&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| [**SAP S/4HANA 2022 FPS02, Fully-Activated Appliance**](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/applianceTemplates/983008db-db92-4d4d-ac79-7e2afa95a2e0)| July 16 2023 |This appliance contains SAP S/4HANA 2022 (FPS02) with pre-activated SAP Best Practices for SAP S/4HANA core functions, and further scenarios for Service, Master Data Governance (MDG), Portfolio Mgmt. (PPM), Human Capital Management (HCM), Analytics, and more. User access happens via SAP Fiori, SAP GUI, SAP HANA Studio, Windows remote desktop, or the backend operating system for full administrative access. |  [Create Appliance](https://cal.sap.com/registration?sguid=983008db-db92-4d4d-ac79-7e2afa95a2e0&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8)
| [**SAP S/4HANA 2023 FPS01**](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/applianceTemplates/5ea7035f-4ea5-4245-bde5-3fff409a2f03) | March 12 2024 |This Appliance Template contains a pre-configured and activated SAP S/4HANA Fiori UI in client 100, with prerequisite components activated as per SAP note 3336782 – Composite SAP note: Rapid Activation for SAP Fiori in SAP S/4HANA 2023. It also includes a remote desktop for easy frontend access. | [Create Appliance](https://cal.sap.com/registration?sguid=5ea7035f-4ea5-4245-bde5-3fff409a2f03&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| [**SAP Yard Logistics 2021 SP03 for SAP S/4HANA**](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/applianceTemplates/85374cf6-4f40-476c-8864-7f3948f8c7b0)| May 22 2024 |Run more efficient and profitable supply chain logistics with the SAP Yard Logistics application. Maximize your visibility into all yard processes and preview planned workloads with a range of visualization and reporting tools, so you can optimize resource use and support planning, execution, and billing with a single system. | [Create Appliance](https://cal.sap.com/registration?sguid=85374cf6-4f40-476c-8864-7f3948f8c7b0&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| [**SAP Process Orchestration 7.50 SPS28**](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/applianceTemplates/fa69ce73-ba98-438a-b2e9-e0171b280b07) | May 22 2024  | SAP Process Orchestration is a solution offering comprising process integration, business process management and business rules management capabilities. It allows you to model, run and monitor integration scenarios that can span applications from SAP and other vendors. | [Create Appliance](https://cal.sap.com/registration?sguid=fa69ce73-ba98-438a-b2e9-e0171b280b07&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |






  




## Deployment of S/4HANA system for productive usage through SAP Cloud Appliance Library

You can now also deploy SAP S/4HANA systems with High Availability (HA), non-HA or single server architecture through SAP Cloud Appliance Library. The offering comprises default SAP S/4HANA software stacks including FPS levels and an integration into Maintenance Planner to enable creation and installation of custom SAP S/4HANA software stacks.
The following links highlight the Product stacks that you can quickly deploy on Azure. Just select “Deploy System”.

| All products | Link |
| -------------- | :--------- |
| **SAP S/4HANA 2023 FPS00 for Productive Deployments** |[Deploy System](https://cal.sap.com/registration?sguid=88f59e31-d776-45ea-811c-1da6577e4d25&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=newInstallation) |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/88f59e31-d776-45ea-811c-1da6577e4d25)
| **SAP S/4HANA 2022 FPS02 for Productive Deployments** | [Deploy System](https://cal.sap.com/registration?sguid=c86d7a56-4130-4459-8060-ffad1a1118ce&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=newInstallation) |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/c86d7a56-4130-4459-8060-ffad1a1118ce) |
| **SAP S/4HANA 2022 FPS01 for Productive Deployments** | [Deploy System](https://cal.sap.com/registration?sguid=1294f31c-2697-443c-bacc-117d5924fcb2&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=newInstallation) |
This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/1294f31c-2697-443c-bacc-117d5924fcb2) |
| **SAP S/4HANA 2022 FPS00 for Productive Deployments**   | [Deploy System](https://cal.sap.com/registration?sguid=3b1dc287-c865-4f79-b9ed-d5ec2dc755e9&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=pd) |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/3b1dc287-c865-4f79-b9ed-d5ec2dc755e9) |
| **SAP S/4HANA 2021 FPS04 for Productive Deployments** | [Deploy System](https://cal.sap.com/registration?sguid=29403c63-6504-4919-b5dd-319d7a99804e&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=newInstallation) |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/29403c63-6504-4919-b5dd-319d7a99804e) |
| **SAP S/4HANA 2021 FPS03 for Productive Deployments** | [Deploy System](https://cal.sap.com/registration?sguid=6921f2f8-169b-45bb-9e0b-d89b4abee1f3&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=pd) |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/6921f2f8-169b-45bb-9e0b-d89b4abee1f3) |
| **SAP S/4HANA 2021 FPS02 for Productive Deployments**   | [Deploy System](https://cal.sap.com/registration?sguid=4d5f19a7-d3cb-4d47-9f44-0a9e133b11de&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=pd)   |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/4d5f19a7-d3cb-4d47-9f44-0a9e133b11de)  |
| **SAP S/4HANA 2021 FPS01 for Productive Deployments**   | [Deploy System](https://cal.sap.com/registration?sguid=1c796928-0617-490b-a87d-478568a49628&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=pd)  |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. | [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/1c796928-0617-490b-a87d-478568a49628)|
| **SAP S/4HANA 2021 FPS00 for Productive Deployments**   | [Deploy System](https://cal.sap.com/registration?sguid=108febf9-5e7b-4e47-a64d-231b6c4c821d&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=pd) |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. | [Details](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/108febf9-5e7b-4e47-a64d-231b6c4c821d) |
| **SAP S/4HANA 2020 FPS04 for Productive Deployments**| [Deploy System](https://cal.sap.com/registration?sguid=615c5c18-5226-4dcb-b0ab-19d0141baf9b&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8&provType=newInstallation) |
|This solution comes as a standard S/4HANA system installation including High Availability capabilities to ensure higher system uptime for productive usage. The system parameters can be customized during initial provisioning according to the requirements for the target system. |  [Details]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/products/615c5c18-5226-4dcb-b0ab-19d0141baf9b) |


 
---

_Within a few hours, a healthy SAP S/4HANA appliance or product is deployed in Azure._

If you bought an SAP CAL subscription, SAP fully supports deployments through SAP CAL on Azure. The support queue is BC-VCM-CAL.
