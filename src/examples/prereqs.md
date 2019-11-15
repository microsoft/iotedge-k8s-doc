# Prerequisites

## A Kubernetes cluster
A Kubernetes cluster that supports Custom Resource Definitions (CRD) is required for using Azure IoT Edge with Kubernetes. **v1.12 or newer is recommended**. Here are some options if you don't already have a cluster available:

For a managed cluster offering, consider Azure Kubernetes Service (AKS). You can stand one up using the [AKS QuickStart](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough) instructions.

For a local development environment, you can use [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/) or [k3d](https://github.com/rancher/k3d#k3s-in-docker) (recommended).

## Helm
Helm is a package manager for Kubernetes which allows you to install applications, including IoT Edge, into your cluster. Please follow the Helm 2 install [insructions](https://v2.helm.sh/docs/using_helm/#installing-helm). 

>💣
> 
>IoT Edge on Kubernetes has only been tested with Helm 2 at this time. Support for Helm 3 will be added in the future.

## kubectl
[Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) is an essential tool for interacting with your cluster.

## Visual Studio Code
[Visual Studio Code](https://code.visualstudio.com/download) with [IoT Edge extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) is recommended for working with IoT Edge's deployment manifests and submitting them to IoT Hub.

## Visualization tools (optional)
Tools like [Octant](https://github.com/vmware-tanzu/octant) and [K9s](https://k9ss.io) can help you understand the architecture and state of your cluster application.
