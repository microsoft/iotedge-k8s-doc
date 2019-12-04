This example demostrates how to back the `edgeHub` module's message store by using persistent volumes *explicitly* via K8s createOptions extensions. It requires a Azure Kubernetes (AKS) cluster with Helm initialized and `kubectl` installed as noted in the prerequisites. You'll also be using VS Code with Azure IoT tools to work with the edge workload (deployment) manifest.

### Setup steps

1. As needed, follow the steps to [register an IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux#register-an-iot-edge-device). Take note of the device connection string.

1. [Set up VS Code and tools](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux#set-up-vs-code-and-tools), associate with IoT Hub from the previous step.

1. [Create an AKS cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#create-aks-cluster) and [connect to it](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough?view=azure-cli-latest#connect-to-the-cluster).

1. Create an Azure File [storage class](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv#create-a-storage-class).

1. Create an Azure File [persistent volume claim](https://docs.microsoft.com/azure/aks/azure-files-dynamic-pv#create-a-persistent-volume-claim) and make note of its name.

1. Follow steps, or a subset as needed, to install edge deployment into the cluster.

    ```bash
    # Add IoT Edge repo
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/staging  
    helm repo update

    # Create K8s namespace
    kubectl create ns pv2

    # Install IoT Edge CRD
    helm install edge-crd edgek8s/edge-kubernetes-crd  

    # Store the device connection string a variable
    export connStr=replace-with-device-connection-string-from-step-1
    ```

1. Deploy the edge workload into the previously created K8s namespace.

    ```bash
    helm install pv-example2 \
      --namespace pv2 \
      --set "deviceConnectionString=$connStr" \
      edgek8s/edge-kubernetes
    ```

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
                  "image": "azureiotedge/azureiotedge-hub:latest",
                  "createOptions": {
    +               "Env": [
    +                 "storageFolder=/storage"
    +               ],
                    "HostConfig": {
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
                    },
    +               "k8s-experimental": {
    +                 "volumes": [{
    +                   "volume": {
    +                     "name": "pvcvol",
    +                     "persistentVolumeClaim": {
    +                       "claimName": "azurefile"
    +                     }
    +                   },
    +                   "volumeMounts": [{
    +                     "name": "pvcvol",
    +                     "mountPath": "/storage"
    +                   }]
    +                 }]
    +               }
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
            "routes": {
              "upstream": "FROM /messages/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
      }
    }
    ```

    [Volume](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#volume-v1-core) and [VolumeMount](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#volumemount-v1-core) API reference have details on allowed values and defaults.

    >🗒
    >
    >*We've used `edgeHub` as an example here, however you can specify K8s extended createOptions for any module in the edge deployment.*

1. Generate the workload deployment config by right-clicking the **deployment.template.json** in the left navigation pane and selecting **Generate IoT Edge Deployment Manifest**. This will generate the minified **deployment.amd64.json** under the **config** directory.

1. Update the configuration for the device by right-clicking **deployment.amd64.json** and selecting **Create Deployment for Single Device**. In the displayed list, choose the device created in step 1 to complete the operation.

1. In a few seconds, you'll see a new `edgeHub` container instantiated with `/storage` location backed by a persistent volume.

    ```bash
    # List persistent volume claims 
    kubectl get pvc -n pv2
    ```

### Cleanup

```bash
# Cleanup
helm del pv-example2 -n pv2 && \
kubectl delete ns pv2
 ``` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this example (IoT Edge CRD will not be deleted).