---
title: Azure Orbital Ground Station - Get started
description: How to get started with Azure Orbital Ground Station, used to communicate with a private satellite or a selection of public satellites.
author: kellydevens
ms.service: azure-orbital
ms.topic: overview
ms.custom: ga
ms.date: 8/4/2023
ms.author: mosagie
# Customer intent: As a satellite operator, I want to learn how to get started with Azure Orbital Ground Station.
---

# Get started with Azure Orbital Ground Station

Azure Orbital Ground Station can be used to communicate with a private satellite or a selection of public satellites.

## Learn about Azure Orbital Ground Station resources

Azure Orbital Ground Station uses three types of Azure resources:
- [Spacecraft](spacecraft-object.md)
- [Contact profile](concepts-contact-profile.md)
- [Contact](concepts-contact.md)

You need to create each of these resources before you can successfully contact your satellite.

A spacecraft resource is a representation of your satellite in Azure and stores links, ephemeris, and licensing information. A contact profile resource stores pass requirements such as links, channels, and network endpoint details.

Contacts are scheduled at a designated time for a particular combination of a spacecraft and contact profile. When you schedule a contact for a spacecraft, a contact resource is created under your spacecraft resource in your resource group.

## Register a spacecraft

[Register a spacecraft](register-spacecraft.md) resource to add it to your subscription. This process includes creating the spacecraft resource and requesting authorization to use this spacecraft according to the spacecraft and ground station licenses.

   > [!NOTE] 
   > Before spacecraft resources can be created and authorized for private satellites, proper regulatory licenses for both satellites and relevant ground stations must be obtained.

## Configure a contact profile

[Configure a contact profile](contact-profile.md) resource for your spacecraft to store details such as channels, links, and endpoint details.

## Schedule a contact

[Schedule a contact](schedule-contact.md) for a particular spacecraft and contact profile.
