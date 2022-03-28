> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

IoT Edge Daemon can be configured with various settings as described below

| Variable Name                        | Value                                                                                                                                     |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| data.enableGetNodesRBAC                   | <ul><li>**true** indicates `iotedged` has permssions to query node type for the purposes of reporting this value to IoT Hub. Setting to `true` will attempt to create a *NodeObserver* cluster role</li><br><br> <li>**false** indicates `iotedged` does not have persmission to query node type information <br><br> Should be set to `false` if `NodeObserver` cluster role cannot be created with your access permissions. </li></ul> |
| data.persistentVolumeClaim.storageClassName                     | <ul>Storage class name that backs iotedged data directory. </ul>                                             |
| data.persistentVolumeClaim.name                 | <ul>persistent volume claim name that backs iotedged data directory. </ul>                                         |
| data.persistentVolumeClaim.size | <ul>size, in Mb, of persistent volume claim that backs iotedged data directory </ul>                                        |
