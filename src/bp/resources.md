# Use resources and limits

## Set module CPU/memory limits and/or reservations
In many scenarios, IoT Edge is sharing cluster resources with other user and system
workloads. Therefore, it is highly recommended that modules declare their CPU and 
memory reservations or limits using createOption extensions as demonstrated by the
[tutorial on using resources](../../examples/resources.md). See the [Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) on how to correctly use this. 

Reservations for `iotedged` and `edgeAgent` pods are already specified in the Helm
chart, so you don't need to set them yourself for these two pods.

## Leverage device plugins for using other device resources
If your module needs access to special device hardware like accelerators, use the
device plugin framework. See the [GPU/FPGA access Azure Stack Edge documentation](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-modify-fpga-modules-gpu#resource-usage) as an example.

**Do not mount device paths directly into the module using its createOptions.**