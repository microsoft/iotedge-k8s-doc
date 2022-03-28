> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

# Adjust Deployment strategy

The default module update behavior is `RollingUpdate`. This spins up a new instance
of the module and ensures it is running before tearing down the old instance. This 
is great for reducing module downtime, however there are conditions where this update
strategy does not work.

If a module is using an exclusive resource like a persistent volume of type
`ReadWriteOnce` or a device plugin resource like a GPU, the new instance remains in 
the pending state until the old instance relinquishes the held resource. At the same
time, the old instance is not removed until the new instance starts running. So,
things are deadlocked and the update process becomes stuck indefinitely.

To avoid this condition, adjust the createOptions of a module that is using exclusive
resources by setting its [deployment strategy to `Recreate`](https://github.com/Azure/iotedge/blob/release/1.1-k8s-preview/kubernetes/doc/create-options.md#apply-deployment-strategy).

> 💣
>
> `Recreate` deployment strategy can increase module downtime as the old module instance
> is torn down before the new instance starts running. If the update specifies a new
> container image, for example, the module will remain down until the new image is pulled
> and started.

