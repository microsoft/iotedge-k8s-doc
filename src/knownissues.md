> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)
# Known issues

Some capabilities available on IoT Edge with Docker on a single device are not available in IoT Edge on Kubernetes. 

## Not all Docker API `createOptions` are translated
Only a [subset](translations.html) of Docker options are translated to Kubernetes. This subset is determined
by what is *translatable* in Kubernetes environment and customer usage scenarios.

## Environment variables with *colons* in their name cannot be used
Replace `:` with `_` to conform to Kubernetes environment variable naming requirements.

## Only a single module in a deployment can be started on the host network
Since every module has a sidecar proxy listening on `locahost` ports, currently only
one module in the edge deployment can be started on the host network. However, a module
on the host network can communicate with other modules on the cluster internal
network without any other changes.

## Logging-related edgeAgent direct methods are not available
On Docker-based systems, `edgeAgent` has [logging-related direct methods](https://docs.microsoft.com/azure/iot-edge/how-to-retrieve-iot-edge-logs?view=iotedge-2018-06) that enable experiences such as [troubleshooting from IoT Hub Portal](https://docs.microsoft.com/azure/iot-edge/troubleshoot-in-portal?view=iotedge-2018-06). These direct methods and experiences are not available when running on Kubernetes.

## Built-in metrics from edgeAgent are not available
On Kubernetes, the edgeAgent does not emit [built-in metrics](https://docs.microsoft.com/azure/iot-edge/how-to-access-built-in-metrics?view=iotedge-2018-06#available-metrics). Therefore some of the [curated visualizations](https://docs.microsoft.com/azure/iot-edge/how-to-explore-curated-visualizations?view=iotedge-2018-06&tabs=devices%2Chost#iot-edge-device-details-workbook) are not available.

## Node.js modules do not start
IoT Edge modules running on Kubernetes based on Node.js IoT SDKs are currently not able to successfully retrieve credentials from `iotedged` and so cannot start. 