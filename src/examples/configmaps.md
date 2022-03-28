> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

This example demostrates how you can use Kubernetes configmaps, in an IoT Edge deployment.  It requires a Kubernetes cluster with Helm initailized and `kubectl` installed as noted in the prerequisites. You'll also be using VS Code with Azure IoT tools to work with the edge workload (deployment) manifest.

### Setup steps

1. As needed, follow the steps to [register an IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux#register-an-iot-edge-device). Take note of the device connection string.

1. [Set up VS Code and tools](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux#set-up-vs-code-and-tools), associate with IoT Hub from the previous step.

1. Follow steps, or a subset as needed, to install edge deployment into the cluster.

    > 
    > For simplicity, this tutorial doesn't specify a persistent store for `iotedged` during install. However, for any serious/PoC deployment, follow the best practice example shown in step 6 of [iotedged failure resilience tutorial](./ha.html).

    ```bash

    # Create K8s namespace
    kubectl create ns cm

    # Install IoT Edge CRD, if not already installed
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge-crd edge-kubernetes-crd

    # Store the device connection string in a variable (enclose in single quotes)
    export connStr='replace-with-device-connection-string-from-step-1'

    # Install the edge workload into the cluster namespace
    helm install --repo https://edgek8s.blob.core.windows.net/staging cm-example edge-kubernetes \
      --namespace cm \
      --set "provisioning.deviceConnectionString=$connStr"

    ```

1. In the Visual Studio Code command palette (View menu -> Command Palette...), search for and select **Azure IoT Edge: New IoT Edge Solution**. Follow the prompts and use the following values to create your solution: 

   | Field | Value |
   | ----- | ----- |
   | Select folder | Choose the location on your development machine for VS Code to create the solution files. |
   | Provide a solution name | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Select module template | Choose **Empty solution**. |

   You'll be making updates to **deployment.template.json** (see navigation pane on the left) to configure the `edgeHub` module to use K8s configmaps.

1. Create a configmap in the namespace previously created.

    ```bash
    kubectl create configmap special-config \
      --from-literal=special.how=very \
      --from-literal=special.type=charm \
      --namespace=cm
    ```

1. Reference the configmap in the `createOptions` section of the `edgeHub` module in **deployment.template.json** using [extended createOptions](https://github.com/Azure/iotedge/blob/release/1.1/kubernetes/doc/create-options.md) feature.


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
    +                     "name": "cmvol",
    +                     "configMap": {
    +                       "optional": "true",
    +                       "name": "special-config"
    +                     }
    +                   },
    +                   "volumeMounts": [{
    +                     "name": "cmvol",
    +                     "mountPath": "/etc/module",
    +                     "readOnly": "true"
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
            "routes": {},
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
      }
    } 
    ```

    [Volume](https://v1-18.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#volume-v1-core) and [VolumeMount](https://v1-18.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#volumemount-v1-core) API reference have details on allowed values and defaults.

    >
    >We've used `edgeHub` as an example here, however you can specify K8s extended createOptions for any module in the edge deployment.
    
1. Generate the workload deployment config by right-clicking the **deployment.template.json** in the left navigation pane and selecting **Generate IoT Edge Deployment Manifest**. This will generate the minified **deployment.amd64.json** under the **config** directory.

1. Update the configuration for the device by right-clicking **deployment.amd64.json** and selecting **Create Deployment for Single Device**. In the displayed list, choose the device created in step 1 to complete the operation.

1. In a few seconds, you'll see a new `edgeHub` pod instantiated with the configmap keys mounted as files at the specified location. 

    ```bash
    # Get pod names
    kubectl get pods -n cm

    # Save edgehub pod name in env var
    export ehname=replace-with-edgehub-pod-name

    # List volume mount location
    kubectl exec --namespace=cm $ehname -c edgehub ls /etc/module

    ```

### Cleanup

```bash
# Cleanup
helm del cm-example -n cm && \
kubectl delete ns cm
 ``` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this example (IoT Edge CRD will not be deleted)





