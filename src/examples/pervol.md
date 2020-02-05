## Using Kubernetes Persistent Volumes

[Persistent volumes (PV)](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) are a key construct for stateful applications in Kubernetes. By attaching them to pods, compute can be allowed to "roam" among cluster nodes while preserving their data, *as long as pods store their state in persistent volumes*. To workloads, PVs appear as locations on the filesystem they can use to store and retrieve files.

IoT Edge modules can leverage persistent volumes implicitly via IoT Edge app model translations or explicitly using createOptions extensions. The next two examples with demonstrate how to use PVs to make modules resilient to node failures using either option.