IoT Edge Daemon can be configured with various settings as described below

| Variable Name                        | Value                                                                                                                                     |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| data.enableGetNodesRBAC                   | <ul><li>**true** indicates `iotedged` has permssions to query node type for the purposes of reporting this value to IoT Hub. Setting to `true` will attempt to create a *NodeObserver* cluster role</li><br><br> <li>**false** indicates `iotedged` does not have persmission to query node type information <br><br> Should be set to `false` if `NodeObserver` cluster role cannot be created with your access permissions. </li></ul> |
| data.persistentVolumeClaim.storageClassName                     | <ul>Sstorage class name which backs up the edge daemon data directory. </ul>                                             |
| data.persistentVolumeClaim.name                 | <ul>persistent volume claim name which backs up the edge daemon data directory. </ul>                                         |
| data.persistentVolumeClaim.size | <ul>persistent volume claim which backs up the edge daemon data directory </ul>                                        |
