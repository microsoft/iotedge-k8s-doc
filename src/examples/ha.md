This example demonstrates how to back the `iotedged` pod using persistent volumes. `iotedged` contains certificates and other security state which must be persisted on durable storage in order for the edge deployment to be remain functional should the `iotedged` pod be restarted and/or relocated to another node.

 This tutorial requires a Azure Kubernetes (AKS) cluster with Helm initialized and `kubectl` installed as noted in the prerequisites.

> 
>A persistent volume backed by remote or replicated storage to provide resilience to node failure in a multi-node cluster setup. This example uses `azurefile` but you can use any persistent volume provider. 
>
>Local storage backed persistent volumes provide resilience to pod failure if the new pod happens to land on the same node but does not help in cases where the pod migrates nodes.
>
>See the [prerequisites section](prereqs.html#persistent-storage-for-the-iot-edge-daemon-iotedged) for more details.



### Setup steps

1. As needed, follow the steps to [register an IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux#register-an-iot-edge-device). Take note of the device connection string.

1. [Create an AKS cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#create-aks-cluster) and [connect to it](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#connect-to-the-cluster).

1. Create an Azure File [storage class](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv#create-a-storage-class).

1. Create an Azure File [persistent volume claim](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv#create-a-persistent-volume-claim) and make note of its name.

1. Follow steps, or a subset as needed, to install edge deployment into the cluster.

    ```bash

    # Create K8s namespace
    kubectl create ns pv-iotedged

    # Install IoT Edge CRD, if not already installed
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge-crd edge-kubernetes-crd

    # Store the device connection string in a variable (enclose in single quotes)
    export connStr='replace-with-device-connection-string-from-step-1'

    ```

1. Specify persistent volume details to use for storing `iotedged` data during workload install.

   
    ```bash

    helm install --repo https://edgek8s.blob.core.windows.net/staging pv-iotedged-example edge-kubernetes \
      --namespace pv-iotedged \
      --set "iotedged.data.persistentVolumeClaim.name=azurefile" \
      --set "iotedged.data.persistentVolumeClaim.storageClassName=replace-with-name-noted-in-step-4" \
      --set "iotedged.data.persistentVolumeClaim.size=64m" \
      --set "provisioning.deviceConnectionString=$connStr"

   ```

1. In addition to `iotedged`, the `edgeHub` module's message store should also be a backed by a persistent volume to prevent data loss when deployed in a Kubernetes environment. See [this tutorial](pervol_extensions.html) for the steps on how to do this.

### Cleanup

```bash
# Cleanup
helm del pv-iotedged-example -n pv-iotedged && \
kubectl delete ns pv-iotedged
``` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this tutorial (IoT Edge CRD will not be deleted).