# Prerequisites

## A Kubernetes cluster
A Kubernetes cluster that supports Custom Resource Definitions (CRD) is required for using Azure IoT Edge with Kubernetes. **v1.12 or newer is recommended**. Here are some options if you don't already have a cluster available:

For a managed cluster offering, consider Azure Kubernetes Service (AKS). You can stand one up using the [AKS QuickStart](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough) instructions.

For a local development environment, you can use [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/) or [k3d](https://github.com/rancher/k3d#k3s-in-docker) (recommended).

## Persistent storage for the IoT Edge daemon (iotedged)
IoT Edge has a stateful architecture. Even if the workload modules are stateless, at a minimum, the context store for `iotedged` pod should be backed by non-ephemeral storage. Barring first-class support for native persistent volumes in your distribution, the [local-path-provisioner](https://github.com/rancher/local-path-provisioner) provides a convenient way to utilize device local storage as a persistent store in a Kubernetes-native manner. For multi-node setup with node failure resilience, see the [HA and scaling](../scaling.html#what-is-supported) section.

> 💣
> 
> For simplicity, most tutorials that follow don't specify a persistent store for `iotedged` during install. However, for any serious/PoC deployment, follow the best practice example shown in step 6 of [node failure resilience tutorial](./ha.html#setup-steps) *even in a single node setup* (you can use any persistent volume or storage class, not just `azurefile`).

## Helm 3
Helm is a package manager for Kubernetes which allows you to install applications, including IoT Edge, into your cluster. Please follow the **Helm 3** install [instructions](https://helm.sh/docs/intro/install/). 

## kubectl
[Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) is an essential tool for interacting with your cluster.

## Visual Studio Code
[Visual Studio Code](https://code.visualstudio.com/download) with [IoT Edge extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) is recommended for working with IoT Edge's deployment manifests and submitting them to IoT Hub.

## Visualization tools (optional)
Tools like [Octant](https://github.com/vmware-tanzu/octant) and [K9s](https://k9ss.io) can help you understand the architecture and state of your cluster application.
