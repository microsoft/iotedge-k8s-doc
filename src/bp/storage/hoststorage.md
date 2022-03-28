> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

# Host storage

**Do not mount storage from the host directly into a module.**

Unlike a single device environment, host storage on Kubernetes is ephemeral. 
As pods can be moved between agent nodes, any data that your application 
persists directly on a node will be lost when the pod gets re-scheduled to a 
different node. 

It follows that you should not attempt to directly mount files from the host
either. In fact, this is not supported on Kubernetes. If your module needs to 
read its configuration from a file consider using a [configmap](https://kubernetes.io/docs/concepts/configuration/configmap/)
as demonstrated in a [subsequent tutorial](../../examples/configmaps.html). 

If your modules need to persist data, use Kubernetes [persistent volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
leveraging best practices recommended in the following section.
