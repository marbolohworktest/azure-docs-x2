---
title: "Known issues: Azure IoT Operations Preview"
description: Known issues for the MQTT broker, Layered Network Management, connector for OPC UA, OPC PLC simulator, dataflows, and operations experience web UI.
author: dominicbetts
ms.author: dobett
ms.topic: troubleshooting-known-issue
ms.custom:
  - ignite-2023
ms.date: 09/19/2024
---

# Known issues: Azure IoT Operations Preview

[!INCLUDE [public-preview-note](../includes/public-preview-note.md)]

This article lists the known issues for Azure IoT Operations Preview.

## Deploy and uninstall issues

- If you prefer to have no updates made to your cluster without giving explicit consent, you should disable Arc updates when you enable the cluster. This is due to the fact that some system extensions are automatically updated by the Arc agent. To disable updates, include the `--disable-auto-upgrade` flag as part of the `az connectedk8s connect` command.

- If your deployment fails with the `"code":"LinkedAuthorizationFailed"` error, it means that you don't have **Microsoft.Authorization/roleAssignments/write** permissions on the resource group that contains your cluster.

- Directly editing **SecretProviderClass** and **SecretSync** custom resources in your Kubernetes cluster can break the secrets flow in Azure IoT Operations. For any operations related to secrets, use the operations experience UI.

## MQTT broker

- You can't update the Broker custom resource after the initial deployment. You can't make configuration changes to cardinality, memory profile, or disk buffer.

  As a workaround, when deploying Azure IoT Operations with the [az iot ops init](/cli/azure/iot/ops#az-iot-ops-init) command, you can include the `--broker-config-file` parameter with a JSON configuration file for the MQTT broker. For more information, see [Advanced MQTT broker config](https://github.com/Azure/azure-iot-ops-cli-extension/wiki/Advanced-Mqtt-Broker-Config) and [Configure core MQTT broker settings](../manage-mqtt-broker/howto-configure-availability-scale.md).

- Even though the MQTT broker's [diagnostics](../manage-mqtt-broker/howto-configure-availability-scale.md#configure-mqtt-broker-diagnostic-settings) produces telemetry on its own topic, you might still get messages from the self-test when you subscribe to `#` topic.

- Deployment might fail if the **cardinality** and **memory profile** values are set to be too large for the cluster. To resolve this issue, set the replicas count to `1` and use a smaller memory profile, like `low`.

- If you configured the MQTT broker to use disk backed message buffer with persistent volume option, the broker creates a persistent volume claim (PVC) in the same namespace as the broker. If you uninstall Azure IoT Operations, the PVC isn't deleted automatically. To delete the PVC, run the following command `kubectl delete pvc -n <namespace> <pvc-name>`.

## Azure IoT Layered Network Management Preview

- If the Layered Network Management service doesn't get an IP address while running K3S on Ubuntu host, reinstall K3S without _trafeik ingress controller_ by using the `--disable=traefik` option.

    ```bash
    curl -sfL https://get.k3s.io | sh -s - --disable=traefik --write-kubeconfig-mode 644
    ```

    For more information, see [Networking | K3s](https://docs.k3s.io/networking#traefik-ingress-controller).

- If DNS queries don't resolve to the expected IP address while using [CoreDNS](../manage-layered-network/howto-configure-layered-network.md#configure-coredns) service running on child network level, upgrade to Ubuntu 22.04 and reinstall K3S.

## Connector for OPC UA

- Azure Device Registry asset definitions let you use numbers in the attribute section while OPC supervisor expects only strings.

- When you add a new asset with a new asset endpoint profile to the OPC UA broker and trigger a reconfiguration, the deployment of the `opc.tcp` pods changes to accommodate the new secret mounts for username and password. If the new mount fails for some reason, the pod does not restart and therefore the old flow for the correctly configured assets stops as well.

## OPC PLC simulator

If you create an asset endpoint for the OPC PLC simulator, but the OPC PLC simulator isn't sending data to the MQTT broker, run the following command to set `autoAcceptUntrustedServerCertificates=true` for the asset endpoint:

```bash
ENDPOINT_NAME=<name-of-you-endpoint-here>
kubectl patch AssetEndpointProfile $ENDPOINT_NAME \
-n azure-iot-operations \
--type=merge \
-p '{"spec":{"additionalConfiguration":"{\"applicationName\":\"'"$ENDPOINT_NAME"'\",\"security\":{\"autoAcceptUntrustedServerCertificates\":true}}"}}'
```

> [!CAUTION]
> Don't use this configuration in production or preproduction environments. Exposing your cluster to the internet without proper authentication might lead to unauthorized access and even DDOS attacks.

You can patch all your asset endpoints with the following command:

```bash
ENDPOINTS=$(kubectl get AssetEndpointProfile -n azure-iot-operations --no-headers -o custom-columns=":metadata.name")
for ENDPOINT_NAME in `echo "$ENDPOINTS"`; do \
kubectl patch AssetEndpointProfile $ENDPOINT_NAME \
   -n azure-iot-operations \
   --type=merge \
   -p '{"spec":{"additionalConfiguration":"{\"applicationName\":\"'"$ENDPOINT_NAME"'\",\"security\":{\"autoAcceptUntrustedServerCertificates\":true}}"}}'; \
done
```

If the OPC PLC simulator isn't sending data to the MQTT broker after you create a new asset, restart the OPC PLC simulator pod. The pod name looks like `aio-opc-opc.tcp-1-f95d76c54-w9v9c`. To restart the pod, use the `k9s` tool to kill the pod, or run the following command:

```bash
kubectl delete pod aio-opc-opc.tcp-1-f95d76c54-w9v9c -n azure-iot-operations
```

## Dataflows

- You can't use anonymous authentication for MQTT and Kafka endpoints when you deploy dataflow endpoints from the operations experience UI. The current workaround is to use a YAML configuration file and apply it by using `kubectl`.

- Currently in public preview, adjusting the instance count (instanceCount) in a dataflow profile may result in messages being discarded or duplicated on the destination. At this time, it's recommended to not adjust the instance count for a profile with active dataflows.

- When you create a dataflow, if you set the `dataSources` field as an empty list, the dataflow crashes. The current workaround is to always enter at least one value in the data sources.

- Dataflow custom resources created in your cluster aren't visible in the operations experience UI. This is expected because synchronizing dataflow resources from the edge to the cloud isn't currently supported.

- If you have a dataflow that uses a Fabric OneLake endpoint and you disconnect the cluster from the internet for a duration between 24 and 72 hours, the dataflow might stop working with error "Authentication Failed with Access token validation failed." To resolve this issue, manually restart the dataflow pod by running the following command:

  ```bash
  kubectl delete pod -n azure-iot-operations $(kubectl get pod -n azure-iot-operations | grep dataflow | awk '{print $1}')
  ```