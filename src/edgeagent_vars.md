Edge agent can be configured with various environment variables as described below



                                                                                                                                                                            
| Variable Name                        | Value                                                                                                                                                                                                                                                                 |
|--------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| portMappingServiceType               | <ul><li>**clusterIP** to expose the service on a cluster Internal IP </li> <li> **NodePort** to expose the service on each Node's IP at a static port </li>   <li> **LoadBalancer** to expore the service externally using a cloud provider's load balancer </li> <ul> |
| backupConfigFilePath                 | <ul> Path of the location for the backup configuration file </ul>                                                                                                                                                                                                     |
| enableK8sServiceCallTracing          | <ul><li>**True** to enable the logging for K8s service API </li> <li>**False** to enable the logging for K8s service API </li>                                                                                                                                        |
| runtimeLogLevel                      | <ul><li> debug </li> <li> info </li> <li> warn </li> <li> error </li></ul>                                                                                                                                                                                            |
| upstreamProtocol                     | <ul><li>**Amqp** for AMQP protocol </li>  <li>**AmqpWs** for the fallback sceanrio </ul>                                                                                                                                                                              |
| runAsNonRoot                         | <ul><li>**True** to run the agent as non-root user </li>  <li>**False** to run the agent as root user </li> <ul>                                                                                                                                                      |
| enableK8sExtensions                  | <ul><li>**True** to enable the module's create options with K8s primitvies </li>  <li> **False** to disable the module's create options with K8s primitvies </li> </ul>                                                                                               |
| enableExperimentalFeatures           | <ul><li>**True** to enable the experimental features </li> <li> **False** to disable the experimental features </li> </ul>                                                                                                                                            |
| storageClassName                     | <ul> Specify the storage class name if you wish the volumes that back persistent storage in modules to use dynamically provisioned PVCs </ul>                                                                                                                         |
| persistentVolumeName                 | <ul>Specify the persistent volume name if you wish the volume that back up the persistent storage in modules to use that PV </ul>                                                                                                                                     |
| persistentVolumeClaimDefaultSizeInMb | <ul>Specify the persistent volume claim size in Mb when creating PVCs</ul>                                                                                                                                                                                            |
                                                                                                                                                                                           

### Examples 


1. Run edge agent as non-root user with experimental features enabled along with K8s extensions

```bash
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edgeAgent.env.runAsNonRoot=true" \
  --set "edgeAgent.env.enableK8sExtensions=true" \
  --set "edgeAgent.env.enableExperimentalFeatures=true" \
  --set "edgeAgent.env.backupConfigFilePath=/tmp/backup.json" \
  edgek8s/edge-kubernetes
```
2. Run edge agent with persistent volume for other modules to use it

```bash
#Create a storage class and use that as Persistent volume 
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edgeAgent.env.persistentVolumeClaimDefaultSizeInMb=5000" \
  --set "edgeAgent.env.storageClassName=azurefile" \
  edgek8s/edge-kubernetes

#Create a persistent volume in the cluster and set a claim for it
 helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edgeAgent.env.persistentVolumeName=pv-agent" \
  --set "edgeAgent.env.persistentVolumeClaimDefaultSizeInMb=5000" \
  edgek8s/edge-kubernetes          
```

3. Run edge agent with different logging options
```bash
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edge.Agent.env.enableK8sServiceCallTracing=true" \
  --set "edge.Agent.env.runtimeLogLevel=true" \
  edgek8s/edge-kubernetes  
```

4. Run edge agent with a different upstream protocol. Default is AmqpWs
```bash
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edge.Agent.env.upStreamProtocol=Amqp" \
  edgek8s/edge-kubernetes  
```

5. Expose edge agent with different port map settings
```bash
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edge.Agent.env.portMappingServiceType=LoadBalancer" \
  edgek8s/edge-kubernetes  
```

```bash
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edge.Agent.env.portMappingServiceType=clusterIP" \
  edgek8s/edge-kubernetes  
```

```bash
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "edge.Agent.env.portMappingServiceType=NodePort" \
  edgek8s/edge-kubernetes  
```