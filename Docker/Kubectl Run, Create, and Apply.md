

- We get three ways to create pods from the kubectl CLI
	- >kubectl run (single pod per command since 1.18)
	- >kubectl create (create some resources via CLI or YML)
	- >kubectl apply (create/update anything via YAML)

> kubectl ==> kube-control
### Pod
A Kubernetes pod is **a collection of one or more Linux® containers, and is the smallest unit of a Kubernetes application**. Any given pod can be composed of multiple, tightly coupled containers (an advanced use case) or just a single container (a more common use case).
### Creating a Pod with kubectl

```sh
$ kubectl version
$ kubectl version --short
```

### Two ways to deploy Pods (containers): Via commands, or via YAML
#### Let's run a pod of the nginx web server!

```sh
$ kubectl run my-nginx --image nginx
```

#### Let's list the pod

```sh
$ kubectl get pods
```
#### More options

```sh
$ kubectl run --help
Create and run a particular image in a pod.

Examples:
  # Start a nginx pod
  kubectl run nginx --image=nginx

  # Start a hazelcast pod and let the container expose port 5701
  kubectl run hazelcast --image=hazelcast/hazelcast --port=5701

  # Start a hazelcast pod and set environment variables "DNS_DOMAIN=cluster" and "POD_NAMESPACE=default" in the
container
  kubectl run hazelcast --image=hazelcast/hazelcast --env="DNS_DOMAIN=cluster" --env="POD_NAMESPACE=default"

  # Start a hazelcast pod and set labels "app=hazelcast" and "env=prod" in the container
  kubectl run hazelcast --image=hazelcast/hazelcast --labels="app=hazelcast,env=prod"

  # Dry run; print the corresponding API objects without creating them
  kubectl run nginx --image=nginx --dry-run=client

  # Start a nginx pod, but overload the spec with a partial set of values parsed from JSON
  kubectl run nginx --image=nginx --overrides='{ "apiVersion": "v1", "spec": { ... } }'

  # Start a busybox pod and keep it in the foreground, don't restart it if it exits
  kubectl run -i -t busybox --image=busybox --restart=Never

  # Start the nginx pod using the default command, but use custom arguments (arg1 .. argN) for that command
  kubectl run nginx --image=nginx -- <arg1> <arg2> ... <argN>

  # Start the nginx pod using a different command and custom arguments
  kubectl run nginx --image=nginx --command -- <cmd> <arg1> ... <argN>

Options:
    --allow-missing-template-keys=true:
        If true, ignore any errors in templates when a field or map key is missing in the template. Only applies to
        golang and jsonpath output formats.

    --annotations=[]:
        Annotations to apply to the pod.

    --attach=false:
        If true, wait for the Pod to start running, and then attach to the Pod as if 'kubectl attach ...' were called.
        Default false, unless '-i/--stdin' is set, in which case the default is true. With '--restart=Never' the exit
        code of the container process is returned.

    --command=false:
        If true and extra arguments are present, use them as the 'command' field in the container, rather than the
        'args' field which is the default.

    --dry-run='none':
        Must be "none", "server", or "client". If client strategy, only print the object that would be sent, without
        sending it. If server strategy, submit server-side request without persisting the resource.

    --env=[]:
        Environment variables to set in the container.

    --expose=false:
        If true, create a ClusterIP service associated with the pod.  Requires `--port`.

    --field-manager='kubectl-run':
        Name of the manager used to track field ownership.

    --image='':
        The image for the container to run.

    --image-pull-policy='':
        The image pull policy for the container.  If left empty, this value will not be specified by the client and
        defaulted by the server.

    -l, --labels='':
        Comma separated labels to apply to the pod. Will override previous values.

    --leave-stdin-open=false:
        If the pod is started in interactive mode or with stdin, leave stdin open after the first attach completes. By
        default, stdin will be closed after the first attach completes.

    -o, --output='':
        Output format. One of: (json, yaml, name, go-template, go-template-file, template, templatefile, jsonpath,
        jsonpath-as-json, jsonpath-file).

    --override-type='merge':
        The method used to override the generated object: json, merge, or strategic.

    --overrides='':
        An inline JSON override for the generated object. If this is non-empty, it is used to override the generated
        object. Requires that the object supply a valid apiVersion field.

    --pod-running-timeout=1m0s:
        The length of time (like 5s, 2m, or 3h, higher than zero) to wait until at least one pod is running

    --port='':
        The port that this container exposes.

    --privileged=false:
        If true, run the container in privileged mode.

    -q, --quiet=false:
        If true, suppress prompt messages.

    --restart='Always':
        The restart policy for this Pod.  Legal values [Always, OnFailure, Never].

    --rm=false:
        If true, delete the pod after it exits.  Only valid when attaching to the container, e.g. with '--attach' or
        with '-i/--stdin'.

    --save-config=false:
        If true, the configuration of current object will be saved in its annotation. Otherwise, the annotation will
        be unchanged. This flag is useful when you want to perform kubectl apply on this object in the future.

    --show-managed-fields=false:
        If true, keep the managedFields when printing objects in JSON or YAML format.

    -i, --stdin=false:
        Keep stdin open on the container in the pod, even if nothing is attached.

    --template='':
        Template string or path to template file to use when -o=go-template, -o=go-template-file. The template format
        is golang templates [http://golang.org/pkg/text/template/#pkg-overview].

    -t, --tty=false:
        Allocate a TTY for the container in the pod.

Usage:
  kubectl run NAME --image=image [--env="key=value"] [--port=port] [--dry-run=server|client] [--overrides=inline-json]
[--command] -- [COMMAND] [args...] [options]
Use "kubectl options" for a list of global command-line options (applies to all commands).
```


##### Pods: Why do they exist?
- Unlike Docker, you can't create a container directly in K8s
- You create Pod(s) (via CLI, YAML, or API)
	- Kubernetes then creates the container(s) inside it
- kubelet tells the container runtime to create containers for you
- Every type of resource to run containers uses pods


#### Creating a Pod with kubectl
- Two ways to deploy Pods (containers): Via commands, or via YAML


#### Commands
```sh
$ kubectl version
$ kubectl run my-nginx --image nginx
$ kubectl get pods # list the pod
$ kubectl get services
$ kubectl get deployments
$ kubectl get all # see all objects | # List all resources in the Kubernetes cluster
$ kubectl create deployment <name-you-want-to-give> --image <image> # for creating the deployment
$ kubectl scale deploy/<your-image-name> --replicas 2 # for creating the replica set
$ kubectl get deploy/<name> # get the deployment for specific
```

### To get all resources across all namespaces, you can use:

```sh
$ kubectl get all --all-namespaces
```

#### What happened when we scaled?
- kubectl scale will *change* the deployment/my-apache record | change the configuration
- Configuration Manager (CM) will see that *only* replica count has changed
- It will change the number of pods in ReplicaSet
- Scheduler sees a new pod is requested, assigns a node
- Kubelet sees a new pod, tells container runtime to start httpd