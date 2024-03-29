> ⚠️ **Deprecation notice**
>
> This preview integration of IoT Edge 1.x and Kubernetes will not be made generally available, and is no longer supported. The recommended way to run IoT Edge 1.x on Kubernetes is noted in the [product's official docs](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-kubernetes?view=iotedge-2020-11)

# Avoid using Docker socket

## Do not mount `docker.sock` into any module!

Some modules in the single device, Docker-engine, scenario mount the Docker 
socket from the host. That is practice not recommended in any scenario since it 
essentially gives the module `root` privileges on the system. 

It is particularly egregious on Kubernetes since many Kubernetes distributions
do not even have the Docker engine installed and rely on a lower level Container
runtime like containerd. Taking a dependency on the Docker socket is guaranteed 
to fail at run time on such clusters.

Getting access to logs and metrics is a common reason for mounting the Docker socket. The 
recommended way to get logs and metrics on a Kubernetes cluster is to install a cluster-wide 
observability solution like [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-analyze).


