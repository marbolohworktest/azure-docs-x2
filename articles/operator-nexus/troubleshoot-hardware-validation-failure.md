---
title: Azure Operator Nexus troubleshooting hardware validation failure
description: Troubleshoot Hardware Validation Failure for Azure Operator Nexus.
ms.service: azure-operator-nexus
ms.custom: troubleshooting
ms.topic: troubleshooting
ms.date: 01/26/2024
author: vnikolin
ms.author: vanjanikolin
---

# Troubleshoot hardware validation failure in Nexus Cluster

This article describes how to troubleshoot a failed server hardware validation. Hardware validation (HWV) is run as part of cluster deploy action and a bare metal replace action. HWV validates a bare metal machine (BMM) by executing test cases against the baseboard management controller (BMC). The Azure Operator Nexus platform is deployed on Dell servers. Dell servers use the integrated Dell remote access controller (iDRAC) which is the equivalent of a BMC.

## Prerequisites

1. Collect the following information:
   - Subscription ID
   - Cluster name
   - Resource group
2. Request access to the Cluster's Log Analytics Workspace (LAW).
3. Access to BMC webui and/or jumpbox that allows running of racadm utility.

## Locating hardware validation results

1. Navigate to cluster resource group in the subscription
2. Expand the cluster Log Analytics Workspace (LAW) resource for the cluster
3. Navigate to the Logs tab
4. Hardware validation results can be fetched with a query against the `HWVal_CL` table as per the following example

:::image type="content" source="media\hardware-validation-cluster-law.png" alt-text="Screenshot of cluster LAW custom table query." lightbox="media\hardware-validation-cluster-law.png":::

## Examining hardware validation results

The Hardware Validation result for a given server includes the following categories.

- system_info
- drive_info
- network_info
- health_info
- boot_info

Expanding `result_detail` for a given category shows detailed results.

## Troubleshooting specific failures

### System info category

* Memory/RAM Related Failure (memory_capacity_GB) (measured in GiB)
    * Memory specs are defined in the SKU. Memory below threshold value indicates missing or failed Dual In-Line Memory Module (DIMM). A failed DIMM would also be reflected in the `health_info` category. The following example shows a failed memory check.

    ```yaml
        {
            "field_name": "memory_capacity_GB",
            "comparison_result": "Fail",
            "expected": "512",
            "fetched": "480"
        }
    ```

    * To check memory information in BMC webui:

        `BMC` -> `System` -> `Memory`

    * To check memory information with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD hwinventory | grep SysMemTotalSize
    ```

    * To troubleshoot a memory problem engage vendor.

* CPU Related Failure (cpu_sockets)
    * CPU specs are defined in the SKU. Failed `cpu_sockets` check indicates a failed CPU or CPU count mismatch. The following example shows a failed CPU check.

    ```yaml
        {
            "field_name": "cpu_sockets",
            "comparison_result": "Fail",
            "expected": "2",
            "fetched": "1"
        }
    ```

    * To check CPU information in BMC webui:

        `BMC` -> `System` -> `CPU`

    * To check CPU information with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD hwinventory | grep PopulatedCPUSockets
    ```

    * To troubleshoot a CPU problem engage vendor.

* Model Check Failure (Model)
    * Failed `Model` check indicates that wrong server is racked in the slot or there's a cabling mismatch. The following example shows a failed model check.

    ```yaml
        {
            "field_name": "Model",
            "comparison_result": "Fail",
            "expected": "R750",
            "fetched": "R650"
        }
    ```

    * To check model information in BMC webui:

        `BMC` -> `Dashboard` - Shows Model

    * To check model information with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD getsysinfo | grep Model
    ```

    * To troubleshoot this problem, ensure that server is racked in the correct location, cabled accordingly, and that the correct IP is assigned.

* Serial Number Check Failure (Serial_Number)
    * The server's serial number, also referred as the service tag, is defined in the cluster. Failed `Serial_Number` check indicates a mismatch between the serial number in the cluster and the actual serial number of the machine. The following example shows a failed serial number check.

    ```yaml
        {
            "field_name": "Serial_Number",
            "comparison_result": "Fail",
            "expected": "1234567",
            "fetched": "7654321"
        }
    ```

    * To check serial number information in BMC webui:

        `BMC` -> `Dashboard` - Shows Service Tag

    * To check serial number information with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD getsysinfo | grep "Service Tag"
    ```

    * To troubleshoot this problem, ensure that server is racked in the correct location, cabled accordingly, and that the correct IP is assigned.

* iDRAC License Check Failure
    * All iDRACs require a perpetual/production iDRAC datacenter or enterprise license. Trial licenses are valid for only 30 days. A failed `iDRAC License Check` indicates that the required iDRAC license is missing. The following examples show a failed iDRAC license check for a trial license and missing license respectively.

    ```yaml
        {
            "field_name": "iDRAC License Check",
            "comparison_result": "Fail",
            "expected": "idrac9 x5 datacenter license or idrac9 x5 enterprise license - perpetual or production",
            "fetched": "iDRAC9 x5 Datacenter Trial License - Trial"
        }
    ```

    ```yaml
        {
            "field_name": "iDRAC License Check",
            "comparison_result": "Fail",
            "expected": "idrac9 x5 datacenter license or idrac9 x5 enterprise license - perpetual or production",
            "fetched": ""
        }
    ```

    * To troubleshoot this problem engage vendor to obtain the correct license. Apply the license using the iDRAC webui in the following location:

        `BMC` -> `Configuration` -> `Licenses`

* Firmware Version Checks
    * Firmware version checks were introduced in release 3.9. The following example shows the expected log for release versions before 3.9.

  ```yaml
      {
          "system_info": {
              "system_info_result": "Pass",
              "result_log": [
                  "Firmware validation not supported in release 3.8"
              ]
          },
      }
  ```

    * Firmware versions are determined based on the `cluster version` value in the cluster object. The following example shows a failed check due to indeterminate cluster version. If this problem is encountered, verify the version in the cluster object.

  ```yaml
      {
          "system_info": {
              "system_info_result": "Fail",
              "result_log": [
                  "Unable to determine firmware release"
              ]
          },
      }
  ```

    * Firmware versions are logged as informational. The following component firmware versions are typically logged (depending on hardware model):
        * BIOS
        * iDRAC
        * Complex Programmable Logic Device (CPLD)
        * RAID Controller
        * Backplane

    * The HWV framework identifies problematic firmware versions and attempts to auto fix. The following example shows a successful iDRAC firmware fix (versions and task ID are illustrational only).

  ```yaml
      {
          "system_info": {
              "system_info_result": "Pass",
              "result_detail": [
                  {
                    "field_name": "Integrated Dell Remote Access Controller - unsupported_firmware_check",
                    "comparison_result": "Pass",
                    "expected": "6.00.30.00 - unsupported_firmware",
                    "fetched": "7.10.30.00"
                  }
              ],
              "result_log": [
                  "Firmware autofix task /redfish/v1/TaskService/Tasks/JID_274085357727 completed"
              ]
          },
      }
  ```

### Drive info category

* Disk Checks Failure
    * Drive specs are defined in the SKU. Mismatched capacity values indicate incorrect drives or drives inserted in to incorrect slots. Missing capacity and type fetched values indicate drives that are failed, missing, or inserted in to incorrect slots.

    ```yaml
        {
            "field_name": "Disk_0_Capacity_GB",
            "comparison_result": "Fail",
            "expected": "893",
            "fetched": "3576"
        }
    ```

    ```yaml
        {
            "field_name": "Disk_0_Capacity_GB",
            "comparison_result": "Fail",
            "expected": "893",
            "fetched": ""
        }
    ```

    ```yaml
        {
            "field_name": "Disk_0_Type",
            "comparison_result": "Fail",
            "expected": "SSD",
            "fetched": ""
        }
    ```

    * To check disk information in BMC webui:

        `BMC` -> `Storage` -> `Physical Disks`

    * To check disk information with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD raid get pdisks -o -p State,Size
    ```

    * To troubleshoot, ensure that disks are inserted in the correct slots. If the problem persists engage vendor.

### Network info category

* Network Interface Cards (NIC) Check Failure
    * Dell server NIC specs are defined in the SKU. A mismatched link status indicates loose or faulty cabling or crossed cables. A mismatched model indicates incorrect NIC card is inserted in to slot. Missing link/model fetched values indicate NICs that are failed, missing, or inserted in to incorrect slots.

    ```yaml
        {
            "field_name": "NIC.Slot.3-1-1_LinkStatus",
            "comparison_result": "Fail",
            "expected": "Up",
            "fetched": "Down"
        }
    ```

    ```yaml
        {
            "field_name": "NIC.Embedded.2-1-1_LinkStatus",
            "comparison_result": "Fail",
            "expected": "Down",
            "fetched": "Up"
        }
    ```

    ```yaml
        {
            "field_name": "NIC.Slot.3-1-1_Model",
            "comparison_result": "Fail",
            "expected": "ConnectX-6",
            "fetched": "BCM5720"
        }
    ```

    ```yaml
        {
            "field_name": "NIC.Slot.3-1-1_LinkStatus",
            "comparison_result": "Fail",
            "expected": "Up",
            "fetched": ""
        }
    ```

    ```yaml
        {
            "field_name": "NIC.Slot.3-1-1_Model",
            "comparison_result": "Fail",
            "expected": "ConnectX-6",
            "fetched": ""
        }
    ```

    * To check NIC information in BMC webui:

        `BMC` -> `System` -> `Network Devices`

    * To check all NIC information with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD hwinventory NIC
    ```

    * To check a specific NIC with racadm provide the Fully Qualified Device Descriptor (FQDD):

     ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD hwinventory NIC.Embedded.1-1-1
    ```

    * To troubleshoot, ensure that servers are cabled correctly and that ports are linked up. Bounce port on the fabric. Perform flea drain. If the problem persists engage vendor.

* NIC Check L2 Switch Information
    * HWV reports L2 switch information for each of the server interfaces. The switch connection ID (switch interface MAC) and switch port connection ID (switch interface label) are informational.

    ```yaml
        {
            "field_name": "NIC.Slot.3-1-1_SwitchConnectionID",
            "comparison_result": "Info",
            "expected": "unknown",
            "fetched": "c0:d6:82:23:0c:7d"
        }
    ```

    ```yaml
        {
            "field_name": "NIC.Slot.3-1-1_SwitchPortConnectionID",
            "comparison_result": "Info",
            "expected": "unknown",
            "fetched": "Ethernet10/1"
        }
    ```

* Cabling Checks for Bonded Interfaces
    * Mismatched cabling is reported in the result_log. Cable check validates that that bonded NICs connect to switch ports with same Port ID. In the following example Peripheral Component Interconnect (PCI) 3/1 and 3/2 connect to "Ethernet1/1" and "Ethernet1/3" respectively on TOR, triggering a failure for HWV.

  ```yaml
      {
          "network_info": {
              "network_info_result": "Fail",
              "result_detail": [
                  {
                      "field_name": "NIC.Slot.3-1-1_SwitchPortConnectionID",
                      "fetched": "Ethernet1/1",
                  },
                  {
                      "field_name": "NIC.Slot.3-2-1_SwitchPortConnectionID",
                      "fetched": "Ethernet1/3",
                  }
              ],
              "result_log": [
                  "Cabling problem detected on PCI Slot 3 - server NIC.Slot.3-1-1 connected to switch Ethernet1/1 - server NIC.Slot.3-2-1 connected to switch Ethernet1/3"
              ]
          },
      }
  ```

    * To fix the issue insert cables in to the correct interfaces.

* iDRAC (BMC) MAC Address Check Failure
    * The iDRAC MAC address is defined in the cluster for each BMM. A failed `iDRAC_MAC` check indicates a mismatch between the iDRAC/BMC MAC in the cluster and the actual MAC address retrieved from the machine.

    ```yaml
        {
            "field_name": "iDRAC_MAC",
            "comparison_result": "Fail",
            "expected": "aa:bb:cc:dd:ee:ff",
            "fetched": "aa:bb:cc:dd:ee:gg"
        }
    ```

    * To troubleshoot this problem, ensure that correct MAC address is defined in the cluster. If MAC is correct in the cluster object, attempt a flea drain. If problem persists ensure that server is racked in the correct location, cabled accordingly, and that the correct IP is assigned.

* Preboot execution environment (PXE) MAC Address Check Failure
    * The PXE MAC address is defined in the cluster for each BMM. A failed `PXE_MAC` check indicates a mismatch between the PXE MAC in the cluster and the actual MAC address retrieved from the machine.

    ```yaml
        {
            "field_name": "NIC.Embedded.1-1_PXE_MAC",
            "comparison_result": "Fail",
            "expected": "aa:bb:cc:dd:ee:ff",
            "fetched": "aa:bb:cc:dd:ee:gg"
        }
    ```

    * To troubleshoot this problem, ensure that correct MAC address is defined in the cluster. If MAC is correct in the cluster object, attempt a flea drain. If problem persists ensure that server is racked in the correct location, cabled accordingly, and that the correct IP is assigned.

### Health info category

* Health Check Sensor Failure
    * Server health checks cover various hardware component sensors. A failed health sensor indicates a problem with the corresponding hardware component. The following examples indicate fan, drive, and CPU failures respectively.

    ```yaml
        {
            "field_name": "System Board Fan1A",
            "comparison_result": "Fail",
            "expected": "Enabled-OK",
            "fetched": "Enabled-Critical"
        }
    ```

    ```yaml
        {
            "field_name": "Solid State Disk 0:1:1",
            "comparison_result": "Fail",
            "expected": "Enabled-OK",
            "fetched": "Enabled-Critical"
        }
    ```

    ```yaml
        {
            "field_name": "CPU.Socket.1",
            "comparison_result": "Fail",
            "expected": "Enabled-OK",
            "fetched": "Enabled-Critical"
        }
    ```

     * To check health information in BMC webui:

        `BMC` -> `Dashboard` - Shows Health Information

    * To check health information with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD getsensorinfo
    ```

    * To troubleshoot a server health failure engage vendor.

* Health Check LifeCycle (LC) Log Failures
    * Dell server health checks fail for recent Critical LC Log Alarms. The hardware validation plugin logs the alarm ID, name, and timestamp. Recent critical alarms indicate need for further investigation. The following example shows a failure for a critical backplane voltage alarm.

    ```yaml
        {
            "field_name": "LCLog_Critical_Alarms",
            "comparison_result": "Fail",
            "expected": "No Critical Errors",
            "fetched": "53539 2023-07-22T23:44:06-05:00 The system board BP1 PG voltage is outside of range."
        }
    ```

    * Virtual disk errors typically indicate a RAID cleanup false positive condition and are logged due to the timing of raid cleanup and system power off pre HWV. The following example shows an LC log critical error on virtual disk 238. If multiple errors are encountered blocking deployment, delete cluster, wait two hours, then reattempt cluster deployment. If the failures aren't deployment blocking, wait two hours then run BMM replace.
    * Virtual disk errors are allowlisted starting with release 3.13 and don't trigger a health check failure.

    ```yaml
        {
            "field_name": "LCLog_Critical_Alarms",
            "comparison_result": "Fail",
            "expected": "No Critical Errors",
            "fetched": "104473 2024-07-26T16:05:19-05:00 Virtual Disk 238 on RAID Controller in SL 3 has failed."
        }
    ```

    * Allow listed critical alarms and warning alarms are logged as informational starting with Nexus release 3.14.

    ```yaml
        {
            "field_name": "LCLog_Warning_Alarms - Non-Failing",
            "comparison_result": "Info",
            "expected": "Warning Alarm",
            "fetched": "104473 2024-07-26T16:05:19-05:00 The Embedded NIC 1 Port 1 network link is down."
        }
    ```

    * To check LC logs in BMC webui:

        `BMC` -> `Maintenance` -> `Lifecycle Log`

    * To check LC log critical alarms with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD lclog view -s critical
    ```

    * If `Backplane Comm` critical errors are logged, perform flea drain. Engage vendor to troubleshoot any other LC log critical failures.

* Health Check Server Power Control Action Failures
    * Dell server health checks fail for failed server power-up or failed iDRAC reset. A failed server control action indicates an underlying hardware issue. The following example shows failed power on attempt.

    ```yaml
        {
            "field_name": "Server Control Actions",
            "comparison_result": "Fail",
            "expected": "Success",
            "fetched": "Failed"
        }
    ```

    ```yaml
        "result_log": [
          "Server power up failed with: server OS is powered off after successful power on attempt",
        ]
    ```

    * To power server on in BMC webui:

        `BMC` -> `Dashboard` -> `Power On System`

    * To power server on with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD serveraction powerup
    ```

    * To troubleshoot server power-on failure attempt a flea drain. If problem persists engage vendor.

* Virtual Flea Drain
    * HWV attempts a virtual flea drain for most failing checks. Flea drain attempts are logged under `health_info` -> `result_log`.

    ```yaml
        "result_log": [
          "flea drain completed successfully",
        ]
    ```

    * If the virtual flea drain fails, perform a physical flea drain as a first troubleshooting step.

* RAID cleanup failures
    * As part of RAID cleanup, the RAID controller configuration is reset. Dell server health check fails for RAID controller reset failure. A failed RAID cleanup action indicates an underlying hardware issue. The following example shows a failed RAID controller reset.

    ```yaml
        {
            "field_name": "Server Control Actions",
            "comparison_result": "Fail",
            "expected": "Success",
            "fetched": "Failed"
        }
    ```

    ```yaml
        "result_log": [
          "RAID cleanup failed with: raid deletion failed after 2 attempts",
        ]
    ```

    * To clear RAID in BMC webui:

        `BMC` -> `Dashboard` -> `Storage` -> `Controllers` -> `Actions` -> `Reset Configuration`

    * To clear RAID with racadm check for RAID controllers then clear config:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD storage get controllers | grep "RAID"
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BC_PWD storage resetconfig:RAID.SL.3-1         #substitute with RAID controller from get command
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BC_PWD jobqueue create RAID.SL.3-1 --realtime  #substitute with RAID controller from get command
    ```

    * To troubleshoot RAID cleanup failure check for any errors logged. For Dell R650/660, ensure that only slots 0 and 1 contain physical drives. For Dell R750/760, ensure that only slots 0 through 3 contain physical drives. For any other models, confirm there are no extra drives inserted based on SKU definition. All extra drives should be removed to align with the SKU. If the problem persists engage vendor.
    * BMC virtual disk critical alerts triggered during HWV can be ignored.

* Health Check Power Supply Failure and Redundancy Considerations
    * Dell server health checks warn when one power supply is missing or failed. Power supply "field_name" might be displayed as 0/PS0/Power Supply 0 and 1/PS1/Power Supply 1 for the first and second power supplies respectively. A failure of one power supply doesn't trigger an HWV device failure.

    ```yaml
        {
            "field_name": "Power Supply 1",
            "comparison_result": "Warning",
            "expected": "Enabled-OK",
            "fetched": "UnavailableOffline-Critical"
        }
    ```

    ```yaml
        {
            "field_name": "System Board PS Redundancy",
            "comparison_result": "Warning",
            "expected": "Enabled-OK",
            "fetched": "Enabled-Critical"
        }
    ```

    * To check power supplies in BMC webui:

        `BMC` -> `System` -> `Power`

    * To check power supplies with racadm:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD getsensorinfo | grep PS
    ```

    * Reseating the power supply might fix the problem. If alarms persist engage vendor.

### Boot info category

* Boot Device Name Check Considerations
    * The `boot_device_name` check is currently informational.
    * Mismatched boot device name shouldn't trigger a device failure.

    ```yaml
        {
            "field_name": "boot_device_name",
            "comparison_result": "Info",
            "expected": "NIC.PxeDevice.1-1",
            "fetched": "NIC.PxeDevice.1-1"
        }
    ```

* PXE Device Checks Considerations
    * This check validates the PXE device settings.
    * Starting with the 2024-07-01 GA API version, HWV attempts to auto fix the BIOS boot configuration.
    * Failed `pxe_device_1_name` or `pxe_device_1_state` checks indicate a problem with the PXE configuration.
    * Failed settings need to be fixed to enable system boot during deployment.

    ```yaml
        {
            "field_name": "pxe_device_1_name",
            "comparison_result": "Fail",
            "expected": "NIC.Embedded.1-1-1",
            "fetched": "NIC.Embedded.1-2-1"
        }
    ```

    ```yaml
        {
            "field_name": "pxe_device_1_state",
            "comparison_result": "Fail",
            "expected": "Enabled",
            "fetched": "Disabled"
        }
    ```

    * To update the PXE device state and name in BMC webui, set the value then select `Apply` followed by `Apply And Reboot`:

        `BMC` -> `Configuration` -> `BIOS Settings` -> `Network Settings` -> `PXE Device1` -> `Enabled`  
        `BMC` -> `Configuration` -> `BIOS Settings` -> `Network Settings` -> `PXE Device1 Settings` -> `Interface` -> `Embedded NIC 1 Port 1 Partition 1`  
    
    * To update the PXE device state and name with racadm run the following commands:

    ```bash
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD set bios.NetworkSettings.PxeDev1EnDis Enabled
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD set bios.PxeDev1Settings.PxeDev1Interface NIC.Embedded.1-1-1
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD jobqueue create BIOS.Setup.1-1
        racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD serveraction powercycle
    ```

### Device login check

* Device Login Check Considerations
    * The `device_login` check fails if the iDRAC isn't reachable or if the hardware validation plugin isn't able to sign-in.

    ```yaml
        {
            "device_login": "Fail - Unreachable"
        }
    ```

    ```yaml
        {
            "device_login": "Fail - Unauthorized"
        }
    ```

    * To set password in BMC webui:

        `BMC` -> `iDRAC Settings` -> `Users` -> `Local Users` -> `Edit`

    * To set password with racadm:

    ```bash
        racadm -r $BMC_IP -u $BMC_USER -p $CURRENT_PASSWORD  set iDRAC.Users.2.Password $BMC_PWD
    ```

    * To troubleshoot, ping the iDRAC from a jumpbox with access to the BMC network. If iDRAC pings check that passwords match.

## Adding servers back into the Cluster after a repair

After Hardware is fixed, run BMM Replace following instructions from the following page [BMM actions](howto-baremetal-functions.md).

If you still have questions, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
For more information about Support plans, see [Azure Support plans](https://azure.microsoft.com/support/plans/response/).
