# Expose module services

## Ensure all ports required for communication are specified under `PortBindings`.

The [expose services within the cluster tutorial](../../examples/services_internal.html) 
details how to expose a module's endpoint(s) to enable communication between modules
in the same namespace. See the [external services tutorial](../../examples/services_external.html) 
for guidance on how to expose services to clients external to the cluster.

The important difference compared to Docker-based deployments is that clients cannot
access ports that are not defined in `PortBindings` section. 

