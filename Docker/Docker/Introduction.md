
#### Daily Needs Commands
	$ docker version
	
	$ docker ps
			-> list the running containers
			
	$ docker rm <id/name> <id/name> <id/name>
			-> to remove multiple containers, but this removes only containers that are stopped
			
	$ docker run -d -p 8800:80 httpd

	$ ps aux
			-> information about running processes
			
	$ docker top <container_name/id>
			-> Display the running processes of a container
			
	$ docker logs <container_name/id>
			-> to log processes running inside the container
			
	$ docker image ls
			-> list the images you have
			
	$ docker --help
			-> to get a list of commands we can use
			
	$ docker inspect <container_name>
			->show metadata about the container (startup config, volumes, networking, etc)
	
	$ docker stats 
			-> performance stats for all containers
	

> ***The --rm flag is there to tell the Docker Daemon to clean up the container and remove the file system after the container exits***
	
			

The default output renders all version information divided into two sections; the `Client` section contains information about the Docker CLI and client components, and the `Server` section contains information about the Docker Engine and components used by the Docker Engine, such as the containerd and runc OCI Runtimes.

For more: https://docs.docker.com/reference/cli/docker/version/

The information shown may differ depending on how you installed Docker and what components are in use.

### [Client and server versions](https://docs.docker.com/reference/cli/docker/version/#client-and-server-versions)

Docker uses a client/server architecture, which allows you to use the Docker CLI on your local machine to control a Docker Engine running on a remote machine, which can be (for example) a machine running in the cloud or inside a virtual machine.

## What is the cURL command?

[Client URL](https://curl.se/) (cURL, pronounced “curl”) is a command line tool that enables data exchange between a device and a server through a terminal. Using this command line interface (CLI), a user specifies a server URL (the location where they want to send a request) and the data they want to send to that server URL.


#### Image vs. Container
- An image is the application we want to run
- A container is an instance of that image running as a process
- You can have many contaienrs running off the same image

#### What happens in 'docker container run'
1. Looks for that image locally in image cache, doesn't find anything
2. Thne looks in remote image repository (defaults to Docker Hub)
3. Downloads the latest version (nginx: latest by default)
4. Creates new container based on that image and prepares to start
5. Gives it a virtual IP on a private n/w inside docker engine
6. Opens up port 80 on host and forwards to port 80 in container
7. Starts container by using the CMD in the image Dockerfile


![[Pasted image 20240223121020.png]]

#### Containers vs. VMs
| Containers aren't Mini-VM's
- They are just processes
- Limited to what resources they can access
- Exit when process stops



## What's Going On In Containers | CLI Process Monitoring

```sh
$ docker container top <containerName>
```
	 Display the running processes of a container

```sh
$ docker container inspect 
```
		Show metadata about the container (startup config, volumes, networking, etc)

```sh
$ docker container stats
```
		Performance stats for all containers




## Getting a Shell inside Containers | No Need for SSH

```sh
$ docker container run -it
```
		Starts new container interactively

```sh
$ docker container exec -it
```
		Run additional command in existing container

> -it 
> -i :  interactive
> 	keep session open to receive terminal input
> -t :  pseudo-tty
> 	simulates a real terminal, like what SSH does
> bash shell :  if run with -it, it will give you a terminal inside the running container

### Different Linux distros in containers

```sh
$ docker container run -it --name proxy nginx bash
```

```sh
$ docker container run exec -it mysql bash
```



## Docker Networks: Concept

- Each container connected to a private virtual n/w "bridge"
- Each virtual n/w routes through NAT firewall on host IP
- All containers on a virtual n/w can talk to each other without -p
- Best practice is to create a new virtual n/w for each app:
	- network "my_web_app" for mysql and php/apache containers
	- network "my_api" for mongo and nodejs containers

```sh
$ docker container run -p
```
		For local dev/testing, networks usually "just work"

```sh
$ docker container port <container-name>
```
		Quick port check with

```sh
$ docker insepct --format {{.NetworkSettings.IPAddress}} <contianer-name>
```
		Return the ip address


## Docker Networks : CLI Management

1. Show networks
```sh
$ docker network ls
```

2. Inspect a network 
```sh
$ docker network inspect <network-name>
```

3. Create a network
```sh
$ docker network create --driver
```

4. Attach a network to container
```sh
$ docker network connect
$ docker network connect <n/w_name> <container-name>
```


5. Detach a network from container
```sh
$ docker network disconnect
```


### Docker Networks : Default Security
- Create your apps so frontend/backend sit on same Docker network
- Their inter-communication never leaves host
- All externally exposed ports closed by default
- You must manually expose via -p, which is better default security!
- This gets even better later with Swarm and Overlay networks


## Docker Networks : DNS and How Containers Find Each Other

#### Docker DNS
- Docker daemon has a built-in DNS server that containers use by default
- Containers shouldn't rely on IP's for inter-communication
- DNS for friendly names is built-in if you use custom networks
- You're using custom n/ws right?
- This gets way easier with Docker Compose in future Section


## What is an Image (And What Isn't)
- App binaries and dependencies
- Metadata about the image data and how to run the image
- Official definition: "An Image is an ordered collection of root filesystem changes and the corresponding execution parameters for use within a container runtime."
- Not a complete OS. No kernel, kernel modules (e.g. drivers)
- Small as one file (your app binary) like a golang static binary
- Big as a Ubuntu distro with apt, and Apache, PHP, and more installed

## The Mighty Hub: Using Docker Hub Registry Images

#### Image and Their Layers
- Images are made up of file system changes and metadata
- Each layer is uniquely identified and only stored once on a host
- This saves storage space on host and transfer time on push/pull
- A container is just a single read/write layer on top of image

```sh
$ docker image ls
```

```sh
$ docker history nginx
$ docker history mysql
```

```sh
$ docker image inspect nginx
```
	-> returns the JSON metadata about the image

## Image Tagging and Pushing to Docker Hub

```sh
kanhaiya@Sasageyo:~$ docker image tag --help

Usage:  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Aliases:
  docker image tag, docker tag
kanhaiya@Sasageyo:~$
```


> Default tag is latest if not specified

> **Official Repositories:** They live at the "root namespace" of the registry, so they don't need account name in front of repo name

```sh
$ docker pull <image-name>:<tag>
```

#### Tag
- The repository name is usually the name of your image, and the tag name is **the label you assign to a specific version of your image**. For example, in the Docker image tag myapp:1.0 , myapp is the repository name and 1.0 is the tag name.

```sh
kanhaiya@Sasageyo:~$ docker image tag --help

Usage:  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Aliases:
  docker image tag, docker tag
```


```sh
$ docker image tag nginx <account-name>/nginx
```


```sh
$ docker image push <image-name>
```


> If you want to create a private image then create a repository first as a private then push an image

## Building Images: The Dockerfile Basics