# Known issues

## Not all Docker API `createOptions` are translated
Only a [subset](../translations.html) of Docker options are translated to Kubernetes. This subset is determined
by what is *translatable* in Kubernetes environment and customer usage scenarios.

## Environment variables with *colons* in their name cannot be used
Replace `:` with `_` to conform to Kubernetes environment variable naming requirements.

## Only a single module in a deployment can be started on the host network
Since every module has a sidecar proxy listening on `locahost` ports, currently only
one module in the edge deployment can be started on the host network. However, a module
on the host network can communicate with other modules on the cluster internal
network without any other changes. 