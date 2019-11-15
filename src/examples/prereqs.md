## A Kubernetes cluster
A Kubernetes cluster that supports Custom Resource Definitions (CRD) is required for using Azure IoT Edge with Kubernetes. However, a relatively recent release (v1.12+) is recommended. Here are the couple of recommendations if you don't already have one available:

For a managed cluster offering, consider Azure Kubernetes Service (AKS). You can get one stood up easily using the [AKS QuickStart](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough) instructions.

For a local development environment, you can use [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/) or [k3d](https://github.com/rancher/k3d#k3s-in-docker) (recommended).

## Helm
Helm is a package manager for Kubernetes which allows you to install applications, including IoT Edge, into your cluster. Please follow in the [insructions](https://v2.helm.sh/docs/using_helm/#installing-helm) to install Helm 2. 

>⚠️ IoT Edge on Kubernetes has only be tested with Helm 2 at this time. Helm 3 will be supported in the future.

## kubectl
Kubectl is tool for viewing and interacting with your cluster. Here are its [install instructions](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Visual Studio Code
[Visual Studio Code](https://code.visualstudio.com/download) with [IoT Edge extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) is recommended for working with IoT Edge's deployment manifests and submitting them to IoT Hub.

## Visualization tools (optional)
Tools like [Octant](https://github.com/vmware-tanzu/octant) and [K9s](https://k9ss.io) make help understand the architecture and state of your IoT Edge application.
