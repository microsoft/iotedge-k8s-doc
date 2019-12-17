## High availability and scaling

While IoT Edge on Kubernetes does provide resilience to node failure, it does **not** yet provide high availablity with manual or auto scaling per the usual Kubernetes definition. In other words, all modules deployed via IoT Edge are currently *singletons* - only single instances are supported.

The primary reason for this is that IoT Edge couples the application model (based on Docker API) with an opinionated programming model (based on Azure IoT SDK). While it is certainly possible to deploy modules that do not use IoT SDK using IoT Edge, but each module get an IoT Hub identity even if it doesn't use it.

The ability to dynamically create new module identities when mulitple replicas are requested is not yet implemented. This is because the programming model is optimized for in-order message delivery which is important for many IoT scenarios. Azure IoT SDK's in-order message delivery guarantee combined with mulitple module replicas is challenging to implement and needs careful design. We intend to explore this use case in the future.

It is however possible to scale modules *mechanically* by specifing multiple modules with different moduleIds but same container image in the IoT Edge deployment and fronting them with an ingress controller at a cluster level.