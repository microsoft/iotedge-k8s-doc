> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

# Expose module services

## Ensure all ports required for communication are specified under `PortBindings`.

The [expose services within the cluster tutorial](../../examples/services_internal.html) 
details how to expose a module's endpoint(s) to enable communication between modules
in the same namespace. See the [external services tutorial](../../examples/services_external.html) 
for guidance on how to expose services to clients external to the cluster.

The important difference compared to Docker-based deployments is that clients cannot
access ports that are not defined in `PortBindings` section. 

