
### Deployment 

Yaml file consisting of how to run the Docker images in a kubernetes cluster

## Master

kube-apiserver

Exposes REST API

Manifest files(YAML)

Cluster store ---> etcd (NOSQL Database) Source of truth 

kube-controller-manager
 1 Node Controller
 2 Endpoints Controller
 3 Name Space Controller

Kube-Schudeler
 Affinity
 Resources(CPU,MEMORY)


## Node

Kubelet 
Kubeproxy
    Networking in Kubernetes
    Loadbalancing
Exposes port 10255 to report back to master
Container Engine



