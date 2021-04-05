# Persistent storage

>💡 For Azure Stack Edge specific guidance, please see the [ASE section](ase.html).

## Use Kubernetes persistent volumes.

If your module needs to persist non-ephemeral data, be sure to use
[Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).
In the namespace for your IoT Edge workloads, pre-create [Persistent Volume
Claims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) 
 (PVC) for modules that require persistence.

## Always configure `iotedged` with a PVC

At a minimum, one PVC is required for `iotedged` to store its crypto material.
On multi-node k8s clusters this PVC cannot be backed by host local storage as 
its stored state needs to be attached to `iotedged` pod even if it gets moved 
to a different agent node. 

*If a new instance of `iotedged` pod cannot access its previously created 
state, it will create new state and starting running. However, all previously 
running modules will fail and will need to be manually restarted to sync up with 
the new `iotedged` instance.*

When installing `iotedged` into the cluster, simply reference the pre-created 
PVC in the `helm install` command like so:

```bash
helm install --repo https://edgek8s.blob.core.windows.net/staging pv-iotedged-example edge-kubernetes \
  --namespace pv-iotedged \
  --set "iotedged.data.persistentVolumeClaim.name=replace-with-PVC-name" \
  --set "provisioning.deviceConnectionString=$connStr"
```

## Configure `edgeHub` with a PVC to avoid message loss

`edgeHub` always persists messages before sending an *ack* to the sender. This 
is what enables offline store-and-forward functionality. If `edgeHub` is not 
configured with a persistent volume for its message store and it restarts (either
on the same node or different node) any undelivered messages stored by the older
instance are lost. 

If your scenario requires no message loss be sure to configure `edgeHub` with 
a PVC. On a multi-node cluster, this persistent volume should be backed by non-host-local storage. 

## Configure modules' persistent storage using createOption extensions

Rather than rely on Docker API translations, it is simplest to use createOption 
extensions to assign pre-created PVCs to modules. The technique is demonstrated 
by [this](../../examples/pervol_extensions.md) tutorial. 

## `ReadWriteOnce` volumes need additional configuration

Volumes can have different [access modes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes). 

For `ReadWriteOnce` volumes deployment strategy should be set to `Recreate`. For 
more details, see [Deployment strategy guidance](../depstrat.html)

## Some StorageClasses may need custom security context

`fsGroup` and `runAsUser` might be need to be set for some storage classes to avoid 
permission errors when accessing the persistent volume. For example, the following
[securityContext](https://github.com/Azure/iotedge/blob/release/1.1-k8s-preview/kubernetes/doc/create-options.md#apply-pod-security-context)
setting were required for the [Blob Storage module](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-store-data-blob?view=iotedge-2018-06) 
to work with [Longhorn](https://longhorn.io) volumes on [K3s](https://k3s.io):

<pre><code>{
  .
  .
  "k8s-experimental": {
    .
    .
    <strong>"securityContext": {
      "fsGroup": "3000",
      "runAsUser": "1000"
    }</strong>
  }
}
</code></pre>