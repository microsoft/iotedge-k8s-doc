> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

This example demostrates how to back the `edgeHub` module's message store by using persistent volumes *implicitly* via app model translations. It requires a Azure Kubernetes (AKS) cluster with Helm initialized and `kubectl` installed as noted in the prerequisites. You'll also be using VS Code with Azure IoT tools to work with the edge workload (deployment) manifest.

### Setup steps

1. As needed, follow the steps to [register an IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux#register-an-iot-edge-device). Take note of the device connection string.

1. [Set up VS Code and tools](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux#set-up-vs-code-and-tools), associate with IoT Hub from the previous step.

1. [Create an AKS cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#create-aks-cluster) and [connect to it](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#connect-to-the-cluster).

1. Create an Azure File [storage class](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv#create-a-storage-class).

1. Follow steps, or a subset as needed, to install edge deployment into the cluster.

    ```bash

    # Create K8s namespace
    kubectl create ns pv1

    # Install IoT Edge CRD, if not already installed
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge-crd edge-kubernetes-crd

    # Store the device connection string in a variable (enclose in single quotes)
    export connStr='replace-with-device-connection-string-from-step-1'

    ```

1. Specify persistent volume details to use in `edgeAgent` module's environment variables during workload install.

    > 
    > For simplicity, this tutorial doesn't specify a persistent store for `iotedged` during install. However, for any serious/PoC deployment, follow the best practice example shown in the [iotedged failure resilience tutorial](./ha.html).

    ```bash

    helm install --repo https://edgek8s.blob.core.windows.net/staging pv-example1 edge-kubernetes \
      --namespace pv1 \
      --set "provisioning.deviceConnectionString=$connStr" \
      --set "edgeAgent.env.persistentVolumeClaimDefaultSizeInMb=5000" \
      --set "edgeAgent.env.storageClassName=azurefile"

    ```

    >With these install options, any edge workload module that specifies a bind type of `volume` in the **createOptions** `HostConfig` section will be backed by a persistent volume [claim](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolumeclaim) on the provided [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/).


1. In the Visual Studio Code command palette (View menu -> Command Palette...), search for and select **Azure IoT Edge: New IoT Edge Solution**. Follow the prompts and use the following values to create your solution: 

   | Field | Value |
   | ----- | ----- |
   | Select folder | Choose the location on your development machine for VS Code to create the solution files. |
   | Provide a solution name | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Select module template | Choose **Empty solution**. |

   Make updates to the **deployment.template.json** (see navigation pane on the left) to configure the `edgeHub` module to use a storage folder backed by a volume.

    ```diff
    {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {}
              }
            },
            "systemModules": {
              "edgeAgent": {
                "type": "docker",
                "settings": {
                  "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                  "createOptions": {}
                }
              },
              "edgeHub": {
                "type": "docker",
                "status": "running",
                "restartPolicy": "always",
                "settings": {
                  "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                  "createOptions": {
    +               "Env": [
    +                 "storageFolder=/storage"
    +               ],
                    "HostConfig": {
    +                 "Mounts": [{
    +                   "Target": "/storage",
    +                   "Source": "message-storage",
    +                   "Type": "volume",
    +                   "ReadOnly": "false"
    +                 }],
                      "PortBindings": {
                        "5671/tcp": [{
                          "HostPort": "5671"
                        }],
                        "8883/tcp": [{
                          "HostPort": "8883"
                        }],
                        "443/tcp": [{
                          "HostPort": "443"
                        }]
                      }
                    }
                  }
                }
              }
            },
            "modules": {}
          }
        },
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {},
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
      }
    }

    ```

1. Generate the workload deployment config by right-clicking the **deployment.template.json** in the left navigation pane and selecting **Generate IoT Edge Deployment Manifest**. This will generate the minified **deployment.amd64.json** under the **config** directory.

1. Update the configuration for the device by right-clicking **deployment.amd64.json** and selecting **Create Deployment for Single Device**. In the displayed list, choose the device created in step 1 to complete the operation.

1. In a few seconds, you'll see a new `edgeHub` container instantiated with `/storage` location backed by a persistent volume.

    ```bash
    # List persistent volume claims 
    kubectl get pvc -n pv1
    ```

### Cleanup

```bash
# Cleanup
helm del pv-example1 -n pv1 && \
kubectl delete ns pv1
 ``` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this example (IoT Edge CRD will not be deleted).