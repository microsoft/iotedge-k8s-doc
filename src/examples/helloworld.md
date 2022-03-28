> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)
This example demonstrates a "Hello, world" scenario of deploying a simulated temperature sensor edge module. It requires a Kubernetes cluster with Helm initialized and `kubectl` installed as noted in the [prerequisites](./prereqs.md).

### Setup steps

1. [Register an IoT Edge device](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#register-an-iot-edge-device) and [deploy the simulated temperature sensor module](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#deploy-a-module). Be sure to note the device's connection string.

1. Create a Kubernetes namespace to install the edge deployment into.

    ```bash
    kubectl create ns helloworld
    ```

1. Install IoT Edge Custom Resource Definition (CRD).

    ```bash
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge-crd edge-kubernetes-crd  
    ```

1. Deploy the edge workload into the previously created K8s namespace.

    > 
    > For simplicity, this tutorial doesn't specify a persistent store for `iotedged` during install. However, for any serious/PoC deployment, follow the best practice example shown in the [iotedged failure resilience tutorial](./ha.html).

    ```bash

    # Store the device connection string in a variable (enclose in single quotes)
    export connStr='replace-with-device-connection-string-from-step-1'

    # Install edge deployment into the created namespace
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge1 edge-kubernetes \
      --namespace helloworld \
      --set "provisioning.deviceConnectionString=$connStr"

    ```

6. In a couple of minutes, you should see the workload modules defined in the edge deploymentment running as pods along with `edgeagent` and `iotedged`. Confirm this using:

    ```bash
    kubectl get pods -n helloworld

    # View the logs from the simlulated temperature sensor module
    kubectl logs -n helloworld replace-with-temp-sensor-pod-name simulatedtemperaturesensor
    ```

### Cleanup

```bash
# Cleanup
helm del edge1 -n helloworld && \
kubectl delete ns helloworld
 ```` 
 ...will remove all the  Kubernetes resources deployed as part of the edge deployment in this example (IoT Edge CRD will not be deleted).




