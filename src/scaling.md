## High availability and scaling

### What is supported

**Resilience to node failure** 

As IoT Edge uses Kubernetes native constructs like Deployments, it benefits from inherent platform capabilities (when configured for HA) to keep workloads running by moving them off unhealthy nodes to healthy ones. This avoids having a single point of failure for mission critical edge workloads.

> ⚠️
>
> To avoid data/context loss in the event of a node failure, Kubernetes needs to be configured to use remote or replicated (non local-only) storage providers. Public cloud vendors like Azure and most managed Kubernetes distributions provide a first-class solution for this. For a custom setup there are a number of OSS providers like NFS, Rook, OpenEBS etc. Read this CNCF [blogpost](https://www.cncf.io/blog/2018/04/19/container-attached-storage-a-primer/) to learn more, and see some of [providers that are supported](https://kubernetes.io/docs/concepts/storage/).

**Scaling by adding modules to the deployment manifest**

It is possible to scale edge modules *mechanically* by specifying modules with different IDs but same container image in the same IoT Edge deployment and fronting them with a reverse proxy.

### What is not supported

IoT Edge on Kubernetes does not yet provide high availability with manual or auto scaling per the usual Kubernetes definition. In other words, all modules deployed via IoT Edge are currently *singletons* - only single instances are supported. 

The primary reason for this is that IoT Edge couples the application model based on Docker API with an opinionated programming model based on Azure IoT SDK. The programming model is optimized for in-order message delivery which is important for many IoT scenarios. While it is certainly possible to deploy modules that do not use IoT SDK using IoT Edge, each module gets an IoT Hub identity anyway even if it doesn't use it.

The ability to dynamically create new module identities when multiple replicas are requested is not yet implemented. IoT Edge's in-order message delivery guarantee combined with multiple module replicas is challenging to implement and needs careful design. We intend to explore this use case in the future.