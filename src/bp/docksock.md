# Docker socket

## Do not mount `docker.sock` into any module!

Some modules in the single device, Docker-engine, scenario mount the Docker 
socket from the host. That is practice not recommended in any scenario since it 
essentially gives the module `root` privileges on the system. 

It is particularly egregious on Kubernetes since many Kubernetes distributions
do not even have the Docker engine installed and rely on a lower level Container
Runtime Interface (CRI). It is guaranteed to fail at run time on such clusters.

Getting access to logs and metrics is a common reason it is done. The 
recommended way to do this on a Kubernetes cluster is to install cluster-wide 
observability solutions like [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-analyze).


