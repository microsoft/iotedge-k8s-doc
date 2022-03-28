> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

## Multi-tenancy

When installed on Kubernetes, IoT Edge is deployed into a specified Kubernetes [namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/). All resources and services required by the deployment are created within this namespace. Therefore, it's possible to install multiple IoT Edge deployments into the same cluster, each in its seperate namespace. 

There is no upper limit enforced on the number of namespaces, this is generally a function of resource capacity of the cluster.