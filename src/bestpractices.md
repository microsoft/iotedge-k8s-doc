> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

IoT Edge on Kubernetes uses the same Docker-based application model built around
edge modules as a single device with Docker engine. 

However, *configuring* the modules to run on 
Kubernetes involve a number of differences compared to a Docker engine 
environment and require a grasp of a few k8s-specific concepts.

The content in the section highlights the major differences to be aware of and 
best practice recommendations informed by real-world customer deployments.
