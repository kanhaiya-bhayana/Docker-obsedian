
#### Test to ensure the version you installed is up-to-date:

```sh
$ kubectl version --client
```

#### Check that kubectl is properly configured by getting the cluster state:

```shell
$ kubectl cluster-info
```
#### Retrieves information about and displays a list of all the nodes in the cluster.

```sh
$ kubectl get nodes
```

#### Retrieves information about all pods in a cluster.

```sh
$ kubectl get pods 
or
$ kubectl get po

$ kubectl get pods -o wide # for detailed information
```

#### List all namespaces which are available

```sh
$ kubectl get namespaces
```
#### In order to get or list pods running in specific namespace

```sh
$ kubectl get pods --namespace=kube-system
```
# Create pod manually

```sh
$ kubectl run <name-of-the-pod> --image=<name-of-the-docker-image>
```

#### Provides detailed information about a specific pod, including its current state, events, and associated containers.

```sh
$ kubectl describe pod <pod_name>
```
#### Log into the minikube environment (for debugging)

```sh
$ minikube ssh
```
now you can run your docker commands,
like below
![[Pasted image 20240525235143.png]]
#### Deletes a specific pod from the cluster, removing it from the desired state.

```sh
$ kubectl delete pod <pod-name>
```

# Deployments

- Deployments define a desired state for a set of pods (how it should be created or modified) and manage the lifecycle of the pods (when it should be deleted).
- They ensure that a specified number of replicas are running, are efficient at scaling replica pods, can replace existing Deployments and their resources with new ones, and handle updates and rollbacks of application versions.
#### Creates a new deployment in Kubernetes with a specified name.
```sh
$ kubectl create deployment <deployment-name> --image=<docker-image-name>
```
#### Retrieves information about all the deployments running in the cluster.
```sh
$ kubectl get deployments
or 
$ kuebectl get deployment <deployment-name>
```
#### **kubectl describe deployment <deployment_name>:** Provides detailed information about a specific deployment, including the number of replicas, current status, and pods associated with it.

```sh
$ kubectl describe deployment <deployment-name>
```

*Selectors are used in order to connect pods with deployments*, because in kubernetes pods and deployments are separate objects.
#### Delete deployment
```sh
$ kubectl delete deployment <deployment-name>
```
# Scaling 
#### Scale up deployment, increase the quantity of pods
```sh
$ kubectl scale deployment <deployment-name> --replicas=<quanity>

#Example
$ kubectl scale deployment <deployment-name> --replicas=5
```
#### Scale down deployment - same command will be used, just change the quantity
```sh
$ kubectl scale deployment <deployment-name> --replicas=<quantity>

#Example
$ kubectl scale deployment <deployment-name> --replicas=3
```
# Services

Services enable communication between different pods within or outside the cluster. They provide a stable DNS name and IP address for each Pod to allow them access and interact with one another.

Services ensure a Pod can always find—and communicate with—the Pod providing the functionality (such as image processing or web services) it requires even when a previous Pod (alongside its IP) has been deleted.
## Create a service using cluster IP

- Cluster IP is only available inside of the kubernetes cluster.

```sh
$ kubectl expose deployment <deployment-name> --port=<external-port> --target-port=<container-port>

# example

$ kubectl expose deployment nginx-deployment --port=8080 --target-port=80
```
#### Retrieves information about all the services running in the cluster.
```sh
$ kubectl get services
$ kubectl get svc
```
#### Provides detailed information about a specific service, including its IP address, ports and any associated endpoints.

```sh
$ kubectl describe service my-service
```
 a specific service in the cluster.

```sh
$ kubectl delete service <service-name>
```
#### Deletes all the services in the cluster. The `-o wide` flag shows additional information about the services being deleted.

```sh
$ kubectl delete service -o wide
```