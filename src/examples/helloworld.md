This example demostrates a "Hello, world" scenario of deploying a simulated temperature sensor edge module. It requires a Kubernetes cluster with Helm initialized and `kubectl` installed as noted in the prerequisites.

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

    ```bash

    # Store the device connection string in a variable (enclose in single quotes)
    export connStr='replace-with-device-connection-string-from-step-1'

    # Install edge deployment into the created namespace
    helm install --repo https://edgek8s.blob.core.windows.net/staging edge1 edge-kubernetes \
      --namespace helloworld \
      --set "provisioning.deviceConnectionString=$connStr"

    ```

    >*Setting the `runAsNonRoot` edgeAgent env variable causes all IoT Edge modules to be launched in containers using a non-root user account for improved security. The edgeAgent is always started in a container as a non-root user.*

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




