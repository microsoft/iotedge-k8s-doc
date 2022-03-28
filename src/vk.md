> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

# Compare with virtual-kubelet provider

Running IoT Edge on Kubernetes has  different goals from the [iot-edge-virtual-kubelet-provider](https://github.com/Azure/iot-edge-virtual-kubelet-provider) project. The virtual-kubelet integration is a mechanism to define an edge workload deployment in a Kubernetes-native way and deliver it to IoT Edge devices via the IoT Hub. While IoT Edge on Kubernetes is about running the workloads on a Kubernetes cluster.

In fact they can used together to both define the edge workloads and run them on Kubernetes. However, you do not get to use the full fidelity of the Kubernetes application model with IoT Edge on Kubernetes.

