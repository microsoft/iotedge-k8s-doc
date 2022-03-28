> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

# Work with proxies

If IoT Edge is required to connect via a proxy server, specify its address in the 
install command.

<pre><code>helm install --repo https://edgek8s.blob.core.windows.net/staging example edge-kubernetes \
  --namespace helloworld \
  --set "iotedged.data.persistentVolumeClaim.name=iotedged-data" \
  <strong>--set "iotedged.data.httpsProxy=replace-with-proxy-server-address" \</strong>
  <strong>--set "iotedged.data.noProxy=localhost" \</strong>
  --set "provisioning.deviceConnectionString=$connStr"</code></pre>
  
  Proxy settings are propagated to `edgeAgent` as well in addition to `iotedged`.
  `noProxy=localhost` is required to prevent local communication from passing through the
  proxy server.

  >🗒 On Azure Stack Edge, follow [guidance](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy#configure-web-proxy) from their docs.
  
  ## Modules specify proxy settings in deployment manifest

  All other modules in the IoT Edge deployment that need communicate via the proxy
  server (e.g. `edgeHub`) should follow [guidance](https://docs.microsoft.com/azure/iot-edge/how-to-configure-proxy-support?view=iotedge-2018-06#configure-deployment-manifests) to specify the proxy address in
  the deployment manifest. 
  
  *They should also add a **NO_PROXY** environment variable set to **localhost**.*
  This is an additional requirement when running on Kubernetes because modules 
  are configured to communicate with `iotedged` using `http://localhost`




