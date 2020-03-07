This example demonstrates how to expose an in-cluster [Kubernetes Service](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/) from an IoT Edge module.  It requires a Kubernetes cluster with Helm initialized and `kubectl` installed as noted in the prerequisites. You'll also be using VS Code with Azure IoT tools to work with the edge workload (deployment) manifest.

### Setup steps

1. As needed, follow the steps to [register an IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux#register-an-iot-edge-device). Take note of the device connection string.

1. [Set up VS Code and tools](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux#set-up-vs-code-and-tools), associate with IoT Hub from the previous step.

1. Follow steps, or a subset as needed, to install edge deployment into the cluster.

    ```bash

    # Create K8s namespace
    kubectl create ns internal-service

    # Install IoT Edge CRD, if not already installed
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge-crd edge-kubernetes-crd

    # Store the device connection string a variable (enclose in single quotes)
    export connStr='replace-with-device-connection-string-from-step-1'


    # Install the edge workload into the cluster namespace
    helm install --repo https://edgek8s.blob.core.windows.net/staging internal-service-example edge-kubernetes \
      --namespace internal-service \
      --set "provisioning.deviceConnectionString=$connStr"

    ```

1. In the Visual Studio Code command palette (View menu -> Command Palette...), search for and select **Azure IoT Edge: New IoT Edge Solution**. Follow the prompts and use the following values to create your solution: 

   | Field | Value |
   | ----- | ----- |
   | Select folder | Choose the location on your development machine for VS Code to create the solution files. |
   | Provide a solution name | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Select module template | Choose **Empty solution**. |

   You'll update **deployment.template.json** (see navigation pane on the left) to add a sample module that exposes an in-cluster endpoint as a Kubernetes Service.

1. Add a sample **aspnetapp** module under **edgeAgent's** **properties.desired** section as shown below. 

    >The **PortBindings** section of module's [createOptions](https://docs.docker.com/engine/api/v1.34/#operation/ContainerCreate) is translated to a Kubernetes Service of type **ClusterIP** by default. This type of service is not accessible outside the cluster directly.
    >
    >You can change **HostPort** in the module's **createOptions** to configure the port exposed by the service.

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
                    "HostConfig": {
                      "PortBindings": {
                        "5671/tcp": [
                          {
                            "HostPort": "5671"
                          }
                        ],
                        "8883/tcp": [
                          {
                            "HostPort": "8883"
                          }
                        ],
                        "443/tcp": [
                          {
                            "HostPort": "443"
                          }
                        ]
                      }
                    }
                  }
                }
              }
            },
            "modules": {
    +         "aspnetapp": {
    +           "settings": {
    +             "image": "mcr.microsoft.com/dotnet/core/samples:aspnetapp",
    +             "createOptions": {
    +               "HostConfig": {
    +                 "PortBindings": {
    +                   "80/tcp": [
    +                     {
    +                       "HostPort": "8082"
    +                     }
    +                   ]
    +                 }
    +               }
    +             }
    +           },
    +           "type": "docker",
    +           "version": "1.0",
    +           "status": "running",
    +           "restartPolicy": "always"
    +         }
            }
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

1. In a few seconds, you'll see a new pods and services instantiated as defined in the deployment manifest. Check the Services in the namespace to confirm that there's an entry for **aspnetapp**.

    ```bash
    kubectl get services -n internal-service
    ```

### Cleanup

```bash

# Cleanup
helm del internal-service-example -n internal-service && \
kubectl delete ns internal-service

 ``` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this example (IoT Edge CRD will not be deleted).