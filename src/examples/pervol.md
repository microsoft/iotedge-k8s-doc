> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)
## Using Kubernetes Persistent Volumes

[Persistent volumes (PV)](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) are a key construct for stateful applications in Kubernetes. By attaching them to pods, compute can be allowed to "roam" among cluster nodes while preserving their data, *as long as pods store their state in persistent volumes*. To workloads, PVs appear as locations on the filesystem they can use to store and retrieve files.

IoT Edge modules can leverage persistent volumes implicitly via IoT Edge app model translations or explicitly using createOptions extensions. The next two examples with demonstrate how to use PVs to make modules resilient to node failures using either option.