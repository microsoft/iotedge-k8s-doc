> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

## createOptions extensions

As mentioned previously, IoT Edge on Kubernetes automatically translates IoT Edge's docker-based application model to the Kubernetes native application model. However, there are many concepts in the Kubernetes that cannot be expressed in a docker-based application model. Examples include persistent volumes, node selectors, secrets etc.

In order to leverage some useful Kubernetes specific constructs in an IoT Edge deployment manifest, you can use the createOptions extensions feature. This allows you to use native Kubernetes API format to express requirements in a module's createOptions section.

Here are some key points to take note of regarding this feature:
* Only a small subset of Kubernetes appication model, listed below, are supported.

* When present, the createOptions extensions section is ignored without error when processed by the edgeAgent operating in a non-Kuberenetes mode (i.e. when deployed on a single device with a docker engine).

## Feature enabling

These options will take effect if both variables **ExperimentalFeatures__Enabled** and **ExperimentalFeatures__EnableK8SExtensions** are set to **true** for edgeAgent when operating in Kubernetes mode. 

These are turned on by default in edgek8s/edge-kubernetes Helm chart.

Several examples in the [tutorials](examples.html) section demonstrate how to use the feature.

## Supported extensions

See the [repo docs](https://github.com/Azure/iotedge/blob/release/1.1-k8s-preview/kubernetes/doc/create-options.md) for supported extensions.