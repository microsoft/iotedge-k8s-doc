# Run on host network

For some scenarios, especially those involving the BACNet protocol, workloads need 
to run on the host network namespace. This is not the default configuration for 
Kubernetes, so to run a module on the host network use following createOptions:

```
{
    "HostConfig": {
        "NetworkMode": "host"
    }
}
```

The `edgeAgent` translates these createOptions to setup the module to run in the 
host network namespace on Kubernetes. Unlike Docker-based deployments the `NetworkingConfig`
section is not required. It will be ignored if specified. 

> All modules don't need to run in the host network to be able to communicate with each other.
> For example, a BACNet module running on the host network can connect to `edgeHub`
> module running on the internal cluster network.
