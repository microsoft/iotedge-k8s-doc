This example demostrates how to back the `iotedged` pod using persistent volumes. `iotedged` contains certificates and other security state which must be persisted on durable storage in order for the edge deployment to be remain functional should the `iotedged` pod be restarted and/or relocated to another node.

 This tutorial requires a Azure Kubernetes (AKS) cluster with Helm initialized and `kubectl` installed as noted in the prerequisites.

### Setup steps

1. As needed, follow the steps to [register an IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux#register-an-iot-edge-device). Take note of the device connection string.

1. [Create an AKS cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#create-aks-cluster) and [connect to it](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#connect-to-the-cluster).

1. Create an Azure File [storage class](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv#create-a-storage-class).

1. Create an Azure File [persistent volume claim](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv#create-a-persistent-volume-claim) and make note of its name.

1. Follow steps, or a subset as needed, to install edge deployment into the cluster.

    ```bash
    # Add IoT Edge repo
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/staging  
    helm repo update

    # Create K8s namespace
    kubectl create ns ha-iotedged

    # Install IoT Edge CRD
    helm install edge-crd edgek8s/edge-kubernetes-crd  

    # Store the device connection string a variable
    export connStr=replace-with-device-connection-string-from-step-1
    ```

1. Specify persistent volume details to use for storing `iotedged` data during workload install.

    ```bash
    helm install ha-example \
      --namespace ha-iotedged \
      --set "iotedged.data.persistentVolumeClaim.name=azurefile" \
      --set "iotedged.data.persistentVolumeClaim.storageClassName=replace-with-name-noted-in-step-4" \
      --set "iotedged.data.persistentVolumeClaim.size=64m" \
      --set "deviceConnectionString=$connStr" \
      edgek8s/edge-kubernetes
    ```

1. In addition to `iotedged`, the `edgeHub` module's message store should also be a backed by a persistent volume to prevent data loss when deployed in a Kubernetes environment. See [this tutorial](pervol_extensions.html) for the steps on how to do this.

### Cleanup

```bash
# Cleanup
helm del ha-example -n ha-iotedged && \
kubectl delete ns ha-iotedged
``` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this tutorial (IoT Edge CRD will not be deleted).