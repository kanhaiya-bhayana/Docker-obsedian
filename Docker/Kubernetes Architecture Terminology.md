
## Basic Terms: System Parts
- Kubernetes: The whole orchestration system
	- K8s "k-eights" or Kube for short
- Kubectl: CLI to configure Kubernetes and manage apps
	- Using "cube control" official pronunciation
- Node: Single server in the Kubernetes cluster
- Kubelet: Kubernetes agent running on nodes
- Control Plane: Set of containers that manage the cluster
	- Includes API server, scheduler, controller manager, etcd, and more
	- Sometimes called the "master"



### Kubernetes Container Abstraction
- Pod: one or more containers running together on one Node
	- Basic unit of deployment. Containers are always in pods
- Controller: For creating/updating pods and other objects
	- Many types of controllers including Deployment, ReplicaSet, StatefulSet, DaemonSet, Job, CronJob, etc.
- Service: network endpoint to connect to a pod
- Namespace: Filtered group of objects in cluster
- Secrets, ConfigMaps, and more


>The pod is the basic unit of deployment. We don't technically deploy containers directly inside of Kubernetes. We only deploy pods, and we use things on top of that known as controllers to control those pods.


