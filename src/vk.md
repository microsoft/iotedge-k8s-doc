# Compare with virtual-kubelet provider

Running IoT Edge on Kubernetes has  different goals from the [iot-edge-virtual-kubelet-provider](https://github.com/Azure/iot-edge-virtual-kubelet-provider) project. The virtual-kubelet integration is a mechanism to define an edge workload deployment in a Kubernetes-native way and deliver it to IoT Edge devices via the IoT Hub. While IoT Edge on Kubernetes is about running the workloads on a Kubernetes cluster.

In fact they can used together to both define the edge workloads and run them on Kubernetes. However, you do not get to use the full fidelity of the Kubernetes application model with IoT Edge on Kubernetes.

