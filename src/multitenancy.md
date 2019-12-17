## Multi-tenancy

When installed on Kubernetes, IoT Edge is deployed into a specified Kubernetes [namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/). All resources and services required by the deployment are created within this namespace. Therefore, it's possible to install multiple IoT Edge deployments into the same cluster, each in its seperate namespace. 

There is no upper limit enforced on the number of namespaces, this is generally a function of resource capacity of the cluster.