
## KUBERNETES IS CONTAINER ORCHESTRATION SYSTEM


- Kubernetes, also known as K8s, is an open-source system for 
	- automating deployment, scaling and management of containerized applications.
#### kubectl is an command line interface to interact with Kubernetes.
#### Minikube is a way to try, understand and learn about Kubernetes.

----

K8s evolution - fun part - KUBERNETES
```sh
K UBERNETE S ====> K8s
```


## KUBERTES TAKES CARE OF THE

1. **Automatic Deployment** of the containerized applications across different servers.
2. Distribution of the load across multiple servers
3. **Auto-scaling** of the deployed applications
4. **Monitoring** and **Health check** of the containers
5. Replacement of the failed containers

## SUPPORTED CONTAINER RUNTIMES

- Docker
- CRI-O
- containerd


## POD
	is the Smallest unit in the kubernetes world

A Pod is a Kubernetes abstraction that represents a group of one or more application containers (such as Docker), and some shared resources for those containers. Those resources include:

- Shared storage, as Volumes
- Networking, as a unique cluster IP address
- Information about how to run each container, such as the container image version or specific ports to use


![[module_03_pods.svg]]

![[kubernetes-Pod.webp]]

One container per POD is the most common use case, yea but you can create multiple containers inside one POD.


## KUBERNTES CLUSTER
	consists of NODES


Inside Cluster,
	there is 1 master node and 
						others are known as worker nodes.
	
	Master manages worker nodes
## NODE

	is a server, either VM or physical server

- Inside nodes, there are PODS





## What is Kubernetes

- Kubernetes = popular container orchestrator
- Container Orchestration = Make many servers act like one

## Why Kubernetes

- 