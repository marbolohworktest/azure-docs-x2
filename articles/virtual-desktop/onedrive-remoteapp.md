---
title: Use Microsoft OneDrive with a RemoteApp - Azure Virtual Desktop
description: Learn how to use Microsoft OneDrive with a RemoteApp in Azure Virtual Desktop.
ms.topic: how-to
author: dknappettmsft
ms.author: daknappe
ms.date: 09/09/2024
---

# Use Microsoft OneDrive with a RemoteApp in Azure Virtual Desktop 

You can use Microsoft OneDrive alongside a RemoteApp in Azure Virtual Desktop, allowing users to access and synchronize their files while using a RemoteApp. When a user connects to a RemoteApp, OneDrive can automatically launch as a companion to the RemoteApp. This article describes how to configure OneDrive to automatically launch alongside a RemoteApp in Azure Virtual Desktop.

> [!IMPORTANT]
> You can't use the setting **Start OneDrive automatically when I sign in to Windows** in the OneDrive preferences, which starts OneDrive when a user signs in. Instead, you need to configure OneDrive to launch by configuring a registry value, which is described in this article.

## User experience

Once configured, when a user launches a RemoteApp, the OneDrive icon is integrated in the taskbar of their local Windows device. If a user launches another RemoteApp from the same host pool on the same session host, the same instance of OneDrive is used and another doesn't start.

If your session hosts are joined to Microsoft Entra ID, you can [silently configure user accounts](/sharepoint/use-silent-account-configuration) so users are automatically signed in to OneDrive and start synchronizing straight away. Otherwise, users need to sign in to OneDrive on first use.

The icon for the instance of OneDrive accompanying the RemoteApp in the system tray looks the same as if OneDrive is installed on a local device. You can differentiate the OneDrive icon from the remote session by hovering over the icon where the tooltip includes the word **Remote**.

When a user closes or disconnects from the last RemoteApp they're using on the session host, OneDrive exits within a few minutes, unless the user has the OneDrive Action Center window open.

## Prerequisites

Before you can use OneDrive with a RemoteApp in Azure Virtual Desktop, you need:

- Session hosts in a host pool that:

    - Are running Windows 11, version 23H2 with cumulative and noncumulative updates for Windows 11 ([KB5039302](https://support.microsoft.com/en-us/topic/june-25-2024-kb5039302-os-builds-22621-3810-and-22631-3810-preview-0ab34e3f-bca9-4a52-a1a4-404bf8162f58)) or later installed.
    
    - Have the latest version of FSLogix installed. For more information, see [Install FSLogix applications](/fslogix/how-to-install-fslogix).

## Configure OneDrive to launch with a RemoteApp

To configure OneDrive to launch with a RemoteApp in Azure Virtual Desktop, follow these steps:

1. Download and install the latest version of the [OneDrive sync app](https://www.microsoft.com/microsoft-365/onedrive/download) per-machine on your session hosts. For more information, see [Install the sync app per-machine](/sharepoint/per-machine-installation).

1. If your session hosts are joined to Microsoft Entra ID, [silently configure user accounts](/sharepoint/use-silent-account-configuration) for OneDrive on your session hosts, so users are automatically signed in to OneDrive.

1. On your session hosts:

    1. Install the latest Windows Update (ensure that [KB Article 5039302](https://support.microsoft.com/en-us/topic/june-25-2024-kb5039302-os-builds-22621-3810-and-22631-3810-preview-0ab34e3f-bca9-4a52-a1a4-404bf8162f58) is included) on the session host. 

    2. Set the following policy: 
    
          Local Computer Policy\Computer Configuration\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Remote Session Environment\Enable enhanced shell experience for RemoteApp 

    3. Set the following registry value:

       - **Key**: `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
       - **Type**: `REG_SZ`
       - **Name**: `OneDrive`
       - **Data**: `"C:\Program Files\Microsoft OneDrive\OneDrive.exe" /background`

       You can configure the registry using an enterprise deployment tool such as Intune, Configuration Manager, or Group Policy. Alternatively, to set this registry value using PowerShell, open PowerShell as an administrator and run the following command:
    
       ```powershell
       New-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" -Name OneDrive -PropertyType String -Value '"C:\Program Files\Microsoft OneDrive\OneDrive.exe" /background' -Force
       ```
    
    4. Ensure the Stack on the session host is version 2404.16770 or higher.

    5. Reboot your session host.

## Test OneDrive with a RemoteApp

To test OneDrive with a RemoteApp, follow these steps:

1. Connect to a RemoteApp from the host pool and check that the OneDrive icon can be seen on the task bar of your local Windows device.

1. Check that OneDrive is synchronizing files by opening the OneDrive Action Center. Sign in to OneDrive if you weren't automatically signed in.

1. From the RemoteApp, check that you can access your files from OneDrive.

1. Finally, close the RemoteApp and any others from the same session host, and within a few minutes OneDrive should exit.

## OneDrive recommendations

When using OneDrive with a RemoteApp in Azure Virtual Desktop, we recommend that you configure the following settings using the OneDrive administrative template. For more information, see [Manage OneDrive using Group Policy](/sharepoint/use-group-policy#manage-onedrive-using-group-policy) and [Use administrative templates in Intune](/sharepoint/configure-sync-intune).

- [Allow syncing OneDrive accounts for only specific organizations](/sharepoint/use-group-policy#allow-syncing-onedrive-accounts-for-only-specific-organizations).
- [Use OneDrive files On-Demand](/sharepoint/use-group-policy#use-onedrive-files-on-demand).
- [Silently move Windows known folders to OneDrive](/sharepoint/use-group-policy#silently-move-windows-known-folders-to-onedrive).
- [Silently sign-in users to the OneDrive sync app with their Windows credentials](/sharepoint/use-group-policy#silently-sign-in-users-to-the-onedrive-sync-app-with-their-windows-credentials).
