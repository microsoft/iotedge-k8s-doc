IoT Edge Daemon can be configured with various settings as described below

| Variable Name                        | Value                                                                                                                                     |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| enableGetNodesRBAC                   | <ul><li>**True** to enable node selection for the edge daemon</li> <li>**False** to disable node selection for the edge daemon </li></ul> |
| storageClassName                     | <ul>Specify the storage class name which backs up the edge daemon for High Availability </ul>                                             |
| persistentVolumeName                 | <ul>Specify the persistent volume name which backs up the edge daemon for High Availability </ul>                                         |
| persistentVolumeClaimDefaultSizeInMb | <ul>Specify the persistent volume claim which backs up the edge daemon for High Availability </ul>                                        |

### Examples 
1.  Run edge daemon with persistent volume with storage class


```bash
helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "iotedged.env.persistentVolumeClaimDefaultSizeInMb=5000" \
  --set "iotedged.env.storageClassName=azurefile" \
  edgek8s/edge-kubernetes
```
2. Run edge daemon with persistent volume claim
```bash
 helm install edge1 \
  --namespace helloworld \
  --set "deviceConnectionString=$connStr" \
  --set "iotedged.env.persistentVolumeName=pv-iotedged" \
  --set "iotedged.env.persistentVolumeClaimDefaultSizeInMb=5000" \
  edgek8s/edge-kubernetes          
```


