---
title: Hybrid or air-gapped deployment path for sensor management - Microsoft Defender for IoT
description: Learn about additional steps involved in deploying Microsoft Defender for IoT in a hybrid or air-gapped environment.
ms.topic: install-set-up-deploy
ms.date: 09/19/2023
---

# Deploy hybrid or air-gapped OT sensor management

Microsoft Defender for IoT helps organizations achieve and maintain compliance of their OT environment by providing a comprehensive solution for threat detection and management, including coverage across parallel networks. Defender for IoT supports organizations across the industrial, energy, and utility fields, and compliance organizations like NERC CIP or IEC62443.

Certain industries, such as governmental organizations, financial services, nuclear power operators, and industrial manufacturing, maintain air-gapped networks. Air-gapped networks are physically separated from other, unsecured networks like enterprise networks, guest networks, or the internet. Defender for IoT helps these organizations comply with global standards for threat detection and management, network segmentation, and more.

While digital transformation has helped businesses to streamline their operations and improve their bottom lines, they often face friction with air-gapped networks. The isolation in air-gapped networks provides security but also complicates digital transformation. For example, architectural designs such as Zero Trust, which include the use of multi-factor authentication, are challenging to apply across air-gapped networks.

Air-gapped networks are often used to store sensitive data or control cyber physical systems that are not connected to any external network, making them less vulnerable to cyberattacks. However, air-gapped networks are not completely secure and can still be breached. It's therefore imperative to monitor air-gapped networks to detect and respond to any potential threats.

This article describes the architecture of deploying hybrid and air-gapped security solutions, including the challenges and best practices for securing and monitoring hybrid and air-gapped networks. Instead of keeping all Defender for IoT's maintenance infrastructure contained in a closed architecture, we recommend that you integrate your Defender for IoT sensors into your existing IT infrastructure, including on-site or remote resources. This approach ensures that your security operations run smoothly, efficiently, and are easy to maintain.

## Architecture recommendations

The following image shows a sample, high level architecture of our recommendations for monitoring and maintaining Defender for IoT systems, where each OT sensor connects to multiple security management systems in the cloud or on-premises.

:::image type="content" source="../media/on-premises-architecture/on-premises-architecture.png" alt-text="Diagram of the new architecture for hybrid and air-gapped support." lightbox="../media/on-premises-architecture/on-premises-architecture.png" border="false":::

In this sample architecture, three sensors connect to four routers in different logical zones across the organization. The sensors are located behind a firewall and integrate with local, on-premises IT infrastructure, such as local backup servers, remote access connections through SASE, and forwarding alerts to an on-premises security event and information management (SIEM) system.

In this sample image, communication for alerts, syslog messages, and APIs is shown in a solid black line. On-premises management communication is shown in a solid purple line, and cloud / hybrid management communication is shown in a dotted black line.

The Defender for IoT architecture guidance for hybrid and air-gapped networks helps you to:

- **Use your existing organizational infrastructure** to monitor and manage your OT sensors, reducing the need for additional hardware or software
- **Use organizational security stack integrations** that are increasingly reliable and robust, whether you are on the cloud or on-premises
- **Collaborate with your global security teams** by auditing and controlling access to cloud and on-premises resources, ensuring consistent visibility and protection across your OT environments
- **Boost your OT security system** by adding cloud-based resources that enhance and empower your existing capabilities, such as threat intelligence, analytics, and automation

## Deployment steps

Use the following steps to deploy a Defender for IoT system in an air-gapped or hybrid environment:

1. Complete deploying each OT network sensor according to your plan, as described in [Deploy Defender for IoT for OT monitoring](ot-deploy-path.md).

1. For each sensor, do the following steps:

    - **Integrate with partner SIEM / syslog servers**, including setting up email notifications. For example:

        - [Connect Microsoft Defender for IoT with Microsoft Sentinel](../iot-solution.md)
        - [Investigate and detect threats for IoT devices](../iot-advanced-threat-monitoring.md)
        - [Forward on-premises OT alert information](../how-to-forward-alert-information-to-partners.md)

    - **Use the Defender for IoT API to create management dashboards**. For more information, see [Defender for IoT API reference](../references-work-with-defender-for-iot-apis.md).

    - **Set up a proxy or chained proxies to the management environment**.

    - **Set up health monitoring** using an SNMP MIB server or via CLI. For more information, see:

        - [Set up SNMP MIB health monitoring on an OT sensor](../how-to-set-up-snmp-mib-monitoring.md)
        - [Defender for IoT CLI users and access](../references-work-with-defender-for-iot-cli-commands.md)

    - **Configure access to the server management interface**, such as via iDRAC or iLO.

    - **Configure a backup server**, including configurations to save your backup to an external server. For more information, see [Back up and restore OT network sensors from the sensor console](../back-up-restore-sensor.md).

## Transitioning from a legacy on-premises management console

> [!IMPORTANT]
> The [legacy on-premises management console](../legacy-central-management/legacy-air-gapped-deploy.md) won't be supported or available for download after January 1st, 2025. We recommend transitioning to the new architecture using the full spectrum of on-premises and cloud APIs before this date.
>

Our [current architecture guidance](#architecture-recommendations) is designed to be more efficient, secure, and reliable than using the legacy on-premises management console. The updated guidance has fewer components, which makes it easier to maintain and troubleshoot. The smart sensor technology used in the new architecture allows for on-premises processing, reducing the need for cloud resources and improving performance. The updated guidance keeps your data within your own network, providing better security than cloud computing.

If you're an existing customer using an on-premises management console to manage your OT sensors, we recommend transitioning to the updated architecture guidance. The following image shows a graphical representation of the transition steps to the new recommendations:

:::image type="content" source="../media/on-premises-architecture/transition.png" alt-text="Diagram of the transition from a legacy on-premises management console to the newer recommendations." border="false" lightbox="../media/on-premises-architecture/transition.png":::

- **In your legacy configuration**, all sensors are connected to the on-premises management console.
- **During the transition period**, your sensors remain connected to the on-premises management console while you connect any sensors possible to the cloud.
- **After fully transitioning**, you'll remove the connection to the on-premises management console, keeping cloud connections where possible. Any sensors that must remain air-gapped are accessible directly from the sensor UI.

**Use the following steps to transition your architecture:**

1. For each of your OT sensors, identify the legacy integrations in use and the permissions currently configured for on-premises security teams. For example, what backup systems are in place? Which user groups access the sensor data?

1. Connect your sensors to on-premises, Azure, and other cloud resources, as needed for each site. For example, connect to an on-premises SIEM, proxy servers, backup storage, and other partner systems. You may have multiple sites and adopt a hybrid approach, where only specific sites are kept completely air-gapped or isolated using data-diodes.

    For more information, see the information linked in the [air-gapped deployment procedure](#deployment-steps), as well as the following cloud resources:

    - [Provision sensors for cloud management](provision-cloud-management.md)
    - [OT threat monitoring in enterprise SOCs](../concept-sentinel-integration.md)
    - [Securing IoT devices in the enterprise](../concept-enterprise.md)

1. Set up permissions and update procedures for accessing your sensors to match the new deployment architecture.

1. Review and validate that all security use cases and procedures have transitioned to the new architecture.

1. After your transition is complete, decommission the on-premises management console.

### Retirement timeline of the Central Manager

The on-premises management console will be retired on **January 1, 2025** with the following updates/changes:

- Sensor versions released after **January 1, 2025** won't be managed by an on-premises management console.
- Air-gapped sensor support isn't affected by these changes to the on-premises management console support. We continue to support air-gapped deployments and assist with the transition to the cloud. The sensors retain a full user interface so that they can be used in "lights out" scenarios and continue to analyze and secure the network in the event of an outage.
- Air-gapped sensors that can't <!-- or don't / aren't connected to-->connect to the cloud can be managed directly via the sensor console GUI, CLI, or API.
- Sensor software versions released between **January 1st, 2024 – January 1st, 2025** still support the on-premises management console.

For more information, see [OT monitoring software versions](../release-notes.md).

## Next steps

> [!div class="step-by-step"]
> [Maintain OT network sensors from the sensor console](../how-to-manage-individual-sensors.md)
