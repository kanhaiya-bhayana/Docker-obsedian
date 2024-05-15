[Kubernetes](https://kubernetes.io/docs/concepts/overview/), also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

It groups containers that make up an application into logical units for easy management and discovery. Kubernetes builds upon [15 years of experience of running production workloads at Google](http://queue.acm.org/detail.cfm?id=2898444), combined with best-of-breed ideas and practices from the community.


OR

- Kubernetes = popular container orchestrator
- Container Orchestration = Make many servers act like one
- Runs on top of Docker (usually) as a set of APIs in containers
- Provides API/CLI to mange containers across servers


### Orchestration
Orchestration refers to managing multiple automated tasks to create a dynamic workflow (e.g., deploying an app, connecting it to a network, and integrating it with other systems).


#### Kubernetes or Swarm?
- Both are container orchestration
- Both are solid platforms with vendor backing
- Swarm: Easier to deploy/manage
- Kubernetes: More features and flexibility

##### Advantages of Swarm
- Comes with Docker, single vendor container platform
- Easiest orchestrator to deploy/manage yourself
- Follows 80/20 rule, 20% of features for 80% of use cases
- Runs anywhere Doker does:
	- local, cloud, datacenter
	- ARM, Windows, 32-bit
- Secure by default
- Easier to troubleshoot

> Swarm - recommended for small project and team


### Advantages of Kubernetes
- Clouds will deploy/manage Kubernetes for you
- Infrastructure vendors are making their own distribution
- Widest adoption and community
- Flexible: Covers widest set of use cases
- "Kubernetes first" vendor suppor


