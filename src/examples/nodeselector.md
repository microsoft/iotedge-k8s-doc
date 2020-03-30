This example demonstrates how to assign modules to run on specific nodes. It requires a Kubernetes cluster with Helm initialized and `kubectl` installed as noted in the [prerequisites](./prereqs.md). You'll also be using VS Code with Azure IoT tools to work with the edge workload (deployment) manifest.

### Setup steps

1. As needed, follow the steps to [register an IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux#register-an-iot-edge-device). Take note of the device connection string.

1. [Set up VS Code and tools](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux#set-up-vs-code-and-tools), associate with IoT Hub from the previous step.

1. Follow steps, or a subset as needed, to install edge deployment into the cluster.

    > 
    > For simplicity, this tutorial doesn't specify a persistent store for `iotedged` during install. However, for any serious/PoC deployment, follow the best practice example shown in the [iotedged failure resilience tutorial](./ha.html).

    ```bash

    # Create K8s namespace
    kubectl create ns nodeselector

    # Install IoT Edge CRD, if not already installed
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge-crd edge-kubernetes-crd  

    # Store the device connection string a variable (enclose in single quotes)
    export connStr='replace-with-device-connection-string-from-step-1'

    ```

1. Deploy the edge workload into the previously created K8s namespace.

    ```bash

    helm install --repo https://edgek8s.blob.core.windows.net/staging node-selector-example edge-kubernetes \
      --namespace nodeselector \
      --set "provisioning.deviceConnectionString=$connStr"

    ```

1. List the nodes in your cluster.

    ```bash
    
    kubectl get nodes

    ```

1. Pick one of the nodes and add a label to it like so:

    ```bash

    kubectl label nodes <node-name> edgehub=true 

    ```

1. In the Visual Studio Code command palette (View menu -> Command Palette...), search for and select **Azure IoT Edge: New IoT Edge Solution**. Follow the prompts and use the following values to create your solution: 

   | Field | Value |
   | ----- | ----- |
   | Select folder | Choose the location on your development machine for VS Code to create the solution files. |
   | Provide a solution name | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Select module template | Choose **Empty solution**. |

   Make updates to the **deployment.template.json** (see navigation pane on the left) to configure the `edgeHub` module to schedule on a node with a specific label.

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
    +                 "nodeSelector": {
    +                   "edgehub": "true"
    +                 }
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

    >🗒
    >
    >*We've used `edgeHub` as an example here, however you can specify K8s extended createOptions for any module in the edge deployment.*

1. Generate the workload deployment config by right-clicking the **deployment.template.json** in the left navigation pane and selecting **Generate IoT Edge Deployment Manifest**. This will generate the minified **deployment.amd64.json** under the **config** directory.

1. Update the configuration for the device by right-clicking **deployment.amd64.json** and selecting **Create Deployment for Single Device**. In the displayed list, choose the device created in step 1 to complete the operation.

1. In a few seconds, you'll see a new `edgeHub` container instantiated on the node you added the `edgehub=true` label to. You can confirm this by checking the **NODE** column the edgehub pod in the output from below:

    ```bash
    kubectl get pods -n nodeselector -o wide
    ```

### Cleanup

```bash
# Cleanup
helm del node-selector-example -n nodeselector && \
kubectl delete ns nodeselector
 ``` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this example (IoT Edge CRD will not be deleted).
