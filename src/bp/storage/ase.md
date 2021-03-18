# Azure Stack Edge

Azure Stack Edge (ASE) appliances have IoT Edge running in a Kubernetes environment
 as an option for [compute](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-configure-compute#configure-compute).
ASE provides a way to create [shares](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-manage-shares)
that can be used as storage for IoT Edge modules via createOption translations
as demonstrated in a subsequent [tutorial](../../examples/pervol_translation.html). 

## Always use `Mounts` with type `volume` to reference shares

After creating an ASE share, to assign it to a module use `Mounts` in the module's
`createOptions` of type `volume`. The `Source` value should be the same as the 
ASE share name. `Target` value is the module's filesystem location where the 
volume should be mounted.

<pre><code>"createOptions": {
    "HostConfig": {
         <strong>"Mounts": [
            {
                "Target": "/storage",
                "Source": "message-storage",
                "Type": "volume",
                "ReadOnly": "false"
            }
        ]</strong>
    }
}</code></pre>

> 💣 Note
>
> To avoid message loss the `edgeHub` module should be configured with persistent volumes
> as demonstrated in the subsequent persistent volumes [tutorial](../../examples/pervol_translation.html).
> Ignore the `helm` commands as those are not required on ASE.


