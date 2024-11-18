---
title: Troubleshoot container memory limits
description: Troubleshooting Kubernetes container limits
ms.service: azure-operator-nexus
ms.custom: troubleshooting
ms.topic: troubleshooting
ms.date: 11/01/2024
ms.author: matthewernst
author: matternst7258
---

# Troubleshoot container memory limits

## Alerting for memory limits

It's recommended to have alerts set up for the Operator Nexus cluster to look for Kubernetes pods restarting from OOMKill errors. These alerts allow customers to know if a component on a server is working appropriately.

Metrics exposed to identify memory limits:

| Metric Name                          | Description                                      |
| ------------------------------------ | ------------------------------------------------ |
| Container Restarts                   | `kube_pod_container_status_restarts_total`       |
| Container Status Terminated Reason   | `kube_pod_container_status_terminated_reason`    |
| Container Resource Limits            | `kube_pod_container_resource_limits`             |

`Container Status Terminated Reason` displays the OOMKill reason for impacted pods. 

## Identifying Out of Memory (OOM) pods

Start by identifying any components that are restarting or show OOMKill.

```azcli
az networkcloud baremetalmachine run-read-command --name "<bareMetalMachineName>" \
   --limit-time-seconds 60 \
   --commands "[{command:'kubectl get',arguments:[pods,-n,nc-system]}]" \
   --resource-group "<cluster_MRG>" \
   --subscription "<subscription>"
```

Once identified, a `describe pod` command can determine the status and restart count. 

```azcli
az networkcloud baremetalmachine run-read-command --name "<bareMetalMachineName>" \
   --limit-time-seconds 60 \
   --commands "[{command:'kubectl describe',arguments:[pod,<podName>,-n,nc-system]}]" \
   --resource-group "<cluster_MRG>" \
   --subscription "<subscription>"
```

At the same time, a `get events` command can provide history to see the frequency of pod restarts.

```azcli
az networkcloud baremetalmachine run-read-command --name "<bareMetalMachineName>" \
   --limit-time-seconds 60 \
   --commands "[{command:'kubectl get',arguments:[events,-n,nc-system,|,grep,<podName>]}]" \
   --resource-group "<cluster_MRG>" \
   --subscription "<subscription>"
```

The data from these commands identify whether a pod is restarting due to `OOMKill`.

## Patching memory limits

Raise a Microsoft support request for all memory limit changes for adjustments and support.

> [!WARNING]
> Patching memory limits to a pod are not permanent and can be overwritten if the pod restarts.

## Confirm memory limit changes

When memory limits change, the pods should return to `Ready` state and stop restarting. 

The following commands can be used to confirm the behavior.

```azcli
az networkcloud baremetalmachine run-read-command --name "<bareMetalMachineName>" \
   --limit-time-seconds 60 \
   --commands "[{command:'kubectl get',arguments:[pods,-n,nc-system]}]" \
   --resource-group "<cluster_MRG>" \
   --subscription "<subscription>"
```

```azcli
az networkcloud baremetalmachine run-read-command --name "<bareMetalMachineName>" \
   --limit-time-seconds 60 \
   --commands "[{command:'kubectl describe',arguments:[pod,<podName>,-n,nc-system]}]" \
   --resource-group "<cluster_MRG>" \
   --subscription "<subscription>"
```

## Known services susceptible to OOM issues

* cdi-operator
* vulnerability-operator
* cluster-metadata-operator
