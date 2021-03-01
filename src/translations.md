
Azure IoT Edge's application model is based on the Docker API specifically the [createOptions](https://docs.docker.com/engine/api/v1.40/#operation/ContainerCreate) schema. When running on Kubernetes, the same application model is retained and the runtime performs a number of automatic translations to the  Kubernetes application model. The goal is that these edge applications designed to run on a single node will work with minimal modifications when installed in a Kubernetes cluster. To achieve this, IoT Edge on Kubernetes has to transform the edge deployment into Kubernetes objects which will support module-module communication. 


IoT Edge on Kubernetes creates Namespaces, Deployments, Services, ImagePullSecrets, PersistentVolumeClaims, and ServiceAccounts to establish this framework. 

## Translation details

Please see [repo docs](https://github.com/Azure/iotedge/blob/release/1.1-k8s-preview/kubernetes/doc/edge-deployment-to-k8s-translations.md).