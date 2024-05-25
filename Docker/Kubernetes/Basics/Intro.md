
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






## Installation

1. Install kubectl
2. Install minikube - is a tool that lets you run Kubernetes locally.





## Commands
- minikube help # to get the all list of available commands in minikube

```sh
kanhaiya on Sasageyo via 󱃾 minikube(default) at …/keyrings 

└─>minikube help
minikube provisions and manages local Kubernetes clusters optimized for
development workflows.

Basic Commands:
  start            Starts a local Kubernetes cluster
  status           Gets the status of a local Kubernetes cluster
  stop             Stops a running local Kubernetes cluster
  delete           Deletes a local Kubernetes cluster
  dashboard        Access the Kubernetes dashboard running within the minikube
cluster
  pause            pause Kubernetes
  unpause          unpause Kubernetes

Images Commands:
  docker-env       Provides instructions to point your terminal's docker-cli to
the Docker Engine inside minikube. (Useful for building docker images directly
inside minikube)
  podman-env       Configure environment to use minikube's Podman service
  cache            Manage cache for images
  image            Manage images

Configuration and Management Commands:
  addons           Enable or disable a minikube addon
  config           Modify persistent configuration values
  profile          Get or list the current profiles (clusters)
  update-context   Update kubeconfig in case of an IP or port change

Networking and Connectivity Commands:
  service          Returns a URL to connect to a service
  tunnel           Connect to LoadBalancer services

Advanced Commands:
  mount            Mounts the specified directory into minikube
  ssh              Log into the minikube environment (for debugging)
  kubectl          Run a kubectl binary matching the cluster version
  node             Add, remove, or list additional nodes
  cp               Copy the specified file into minikube

Troubleshooting Commands:
  ssh-key          Retrieve the ssh identity key path of the specified node
  ssh-host         Retrieve the ssh host key of the specified node
  ip               Retrieves the IP address of the specified node
  logs             Returns logs to debug a local Kubernetes cluster
  update-check     Print current and latest version number
  version          Print the version of minikube
  options          Show a list of global command-line options (applies to all
commands).

Other Commands:
  completion       Generate command completion for a shell
  license          Outputs the licenses of dependencies to a directory

Use "minikube <command> --help" for more information about a given command.
```