
Docker

[](https://whimsical.com/)

# Docker

## docker commands

- docker version
- docker --version
- docker run -d -p 8080:80 httpd
- docker ps
- curl localhost:8080

docker commands are categorized into management commands and the syntax to use those is

docker <management command> <sub command>

the old way works as well, type just docker to get a list of all

- docker

- get the list of all possible commands
- the new format of “docker run” is “docker container run”

- docker container run --publish --detach 80:80 nginx

- after running this command we can go to localhotst:80

- docker container ls
- docker container stop <container id/name>
- docker container kill <name/id>

- When a process is misbehaving, docker container stop might not cut it. You might just want the container to exit immediately

- docker container ls -a

- shows all containers that may even be stopped

- docker container run --publish 80:80 --detach --name <name> <image name>

- to specify a name
- a container running in detached mode doesnt give out logs in the terminal to get logs we can

- docker container logs <name>
- docker container stats s<name>

- to get performance stats of containers

- docker container top <name>

- to get details about the running container
- to log processes running inside the container

- docker container --help

- to get a list of commands we can use

- docker container rm <id/name> <id/name> <id/name>

- to remove multiple containers
- but this removes only containers that are stopped

- docker container rm -f <id>

- to forcefully remove the containers that are running

⁠ 

- docker container run --detach --publish 3306:3306 --name mysql --env MYSQL_RANDOM_ROOT_PASSWORD=yes mysql

- this sets a random password and we can see it via the logs command

- docker container logs <name /id>

## commands to see what goes on inside the containers

- docker container top <>

- list of processeses in a container

- docker container inspect <>

- details of how the container started

- docker container stats

- performance stats for all containers

## getting inside a container

- docker container run -it

- starts container interactively
- the it is actually two options

- the t opens a pseudo tty, simulating a real terminal
- the i keeps the STDIN open for the container

root user inside the container, the number is just the container id

- ls -al
- ls -lrt
- exit

- to get out
- this stops the container as well

⁠ 

- check the startup command

- bash

- the default command for nginx is

- nginx -g ‘deamon.....something

- docker container exec -it <container> bash
- docker exec -it -u root <containernme> sh

- to login as root user
- run additional commands in the existing containers

exiting here doesnt stop the container

- docker container run -it --name proxy nginx bash

- we have additional arguments after the image to specify the kind of terminal we want

- docker container start -ai proxy bash

- to start back up an exited container interactively

- docker pull alpine

- to pull down images from docker hub
- default alpine doesnt have bash

- docker image ls

- to list images on your local system

## Docker Networks

- docker container port <container name/id>

- tells what ports are open for that container on your network

when we start a container in the background we are connecting to a private virtual network called the bridge network, each one of those virtual networks route through the NAT firewall on the host IP. Done via the docker deamon configuring the host IP addresson its interface so that containers have access to ou network and the internet through it.

all containers can talk to each other inside our host without using the -p. if i have a SQL server container and and an nginx container they can talk to each other inside the virtual network

⁠ 

all those settings are changeable

- batteries included but removable
- like creating multiple virtual networks (1 per app)
- if we have multiple NICs we can have containers be connected to multiple virtual networks
- or we can have them be connected to no virtual network
- we can skip the virtual network configuration and just use the host IP instead with - -net=host
- we have more abilities via docker network drivers

After the docker installation you have 3 networks by default:

`docker network ls``NETWORK ID NAME DRIVER SCOPE``f3be8b1ef7ce bridge bridge local``fbff927877c1 host host local``023bb5940080 none null local`

if you start a container by default it will be created inside the bridge (docker0) network.

`$ docker run -d jenkins``1498e581cdba jenkins "/bin/tini -- /usr..." 3 minutes ago`

In the dockerfile of jenkins the ports `8080` and `50000` are exposed. Those ports are opened for the container on its bridge network. So everything inside that bridge network can access the container on port `8080` and `50000`. Everything in the bridge network is in the private range of `"Subnet": "172.17.0.0/16",` If you want to access them from the outside you have to map the ports with `-p 8080:8080`. This will map the port of your container to the port of your real server (the host network). So accessing your server on `8080` will route to your bridge-network on port `8080`.

the --net=host flag will open the container in the host network and the host port 8080 will access jenkins (but this works only on linux)

containers can use the same IP as the host but by default that is not true

- docker container inspect --format “{{.NetworkSettings.IPAddress}}” <container-name/id>

- to check the ip of the container, we can also use the grep command
- the “{{.NetworkSettings.IPAddress}}” part comes from the config file

but why do the containers not have the same IP as the host. the containers are connected to the bridge network (virtual) the bridge network is connected to the NAT firewall to get out the -p flag tells the NAT to map the bridge network port to my physical ethernet port.

new containers go on the same bridge network by default and can communicate with each other freely

on any interface in our host we cant have two containers listening to the same port

nginx latest image has removed support for the ping command so we can use nginx:alpine instead

- we can get around by adding the ping util with apt-get

docker network drivers are basically just network types, whenever we create a container docker creates a ethernet interface and connects it to the bridge network.

## cli network management

- docker network create --driver <network name>

- create a network with an optional driver
- when we create a network without a driver it uses the default driver that is the bridge driver

- docker network ls
- docker network inspect <network name>

- spits out config file telling the containers connected to that network

out put of the inspect command

- docker network connect <network name/id> <container name/id>

- connect an already running container to a network so that a new NIC is created on a virtual network for that container
- doing this will put the container on two networks which we can check via :-

- docker container inspect <container-name>

- as it gives the container a new ethernet insterface,IP

- docker network disconnect <network name> <container name>
- docker container run -d --name nginx_mynetwork --network my_network nginx:latest

- to connect a new container to our new network

## DNS - how it affects containers in custom networks and default networks

- DNS are the key to inter-container communications and we cant rely on IP addresses, since they change dynamically
- -link option for the container run command

docker daemon has a built in DNS server that uses the container names by default, every new network in docker get automatic DNS resolution, for every container from every other container in that virtual network, via container names

1. start 2 new container on a new network via

docker container run -d --name nginx --network new_network nginx:alpine

docker container run -d --name nginx_second --network new_network nginx:alpine

4. we can ping a container from another since they are on the same network

docker container exec -it nginx_from ping nginx_to

pinging a container from another noth on the same network

The default bridge network doesnt have the DNS server built into it by default, in the bridge netwok we would have to link containers together via the link flag while creating a container or running the container

its better to just create a new network for your apps

docker compose makes it even easier

- docker container run --rm -it --name ubuntu unbuntu:14:04

- this command will open up a container shell and as soon as we exit out of that container shell the container is discarded

## dns round robin test

this test means we can have two different hosts with dns aliases that respond to the same dns name, for example google.com obviously is served by more than a single server

in docker when we create a network we can have multiple containers respond to the same dns name via the alias

- docker container run --network my_net --network-alias search <image-name>

- run this command two times
- to give container a network and an alias
- alias is an additional DNS name for a container to respond to
- we need to use these since we cant have containers with the same name and we use aliases instead to resolve DNS
- we can also do the following

- docker container run -d <image-name>
- docker network connect --alias search my_net <container name>

- we can use nslookup from an alpine container with the following command
- docker container run --rm --net my_net -it --name alpine alipine:latest

- gets us into the container terminal
- nslookup search

- where search is the alias for multiple containers

nslookup result from containers with the same dns resolution name

## Images

basically the binaries and the dependencies for the app, along with the metadata about the image and how to run the image

official definition -

its an ordered collection of root filesystem changes and the corresponding execution parameters for use within a contianer runtime

doesnt have an OS, uses the host kernel

a version of an image can have more than one tags

tags for the same image

all these tags lead to downloading the same image

when we pull images with multiple tags, docker contianer ls gives an out put like

⁠ 

these are not three different images but same image with different tags and all of them in total are taking up only 182 MB

image layers work on the union file system to present a series of file system changes

a container is essesntially an additional layer on top of an image

- docker image history <image name>

- gives a history of the image layers
- every image starts with a blank image called scratch
- and then every set of file system changes that happen after that are called layers
- some layers may have 0 file size, maybe just meta data change
- each layer has a unique sha hash
- image layers are reused between layers, and we dont download layer we already have
- only one copy of each layer is stored
- same applies to uploading
- when we create a container over an image all docker does is create a new read write layer on top of the image we specified
- if we change any file in the container that was supposed to be in the image then a copy of that file is stored in the container on which the changes are written, and container is just the differences between the current layer’s and the image’s files

⁠ 

- as the files change the created date for each layer is actually different

- the <missing> just means the layers dont have an id, and they live inside the image and the image has an id

- docker image inspect <image-name>

- gives the meta data of the image
- also tells how the image expects to be run
- shows us which ports are exposed and follow which protocol
- shows the environment variables
- the command that will execute when the image is containerized and run

images dont have names they have repositories

<user>/<repo>:<tag>

the three things that indetify an image

- docker image tag <image-name>:<tag> <new-tag>:<tag>

- to give an already existing image a new tag

⁠ 

- it gives the old image a new <user>/<repo> name for me it will be beelzekamibub/nginx

- docker image push

- to push the new named image to docker hub
- to push but before we do we have to login

- docker login

- to login this command writes to the docker config file the auth key to push to docker hub

- docker logout

- to logout

if we want a private repository we have to create the repo first from the GUI and make it private

## dockerfile

- docker build -f some-other-file

- to specify a build using the a file other than the defualt

`FROM debian:bullseye-slim`

all docker files must start with a from

if we truly want an empty container use

`FROM scratch`

we can give our image labels next with

`LABEL maintainer="NGINX Docker Maintainers <docker-maint@nginx.com>"`

to set environment variables we use ENV the env variables maybe optional to the image but required for our app that we want to run

`ENV NGINX_VERSION 1.23.4``ENV NJS_VERSION 0.7.11``ENV PKG_RELEASE 1~bullseye`

each of these stanzas are actually layers in our docker image

we can run shell commands(if linux) with RUN

`RUN <some shell command you want>`

we can use run to install some software that we want using myabe apt-get package manager

use && to chain commands

if we use alot of RUN stanzas for each command it will create a lot of layers so instead we can chain commands using &&

We can use another run stanza to point log files to stdout and std error like so

⁠ 

although the proper way of logging inside a contianer is to not use a log file and there are no syslog services inside a container, docker handles all of our logging for us

all we have to do is to make sure the logs are spit to stdout and std error like the previous RUN stanza does so

docker engine has logging driver that can handle all the logs for all the containers on our host

the next thing we can do is expose tcp and udp ports via the EXPOSE stanza

`EXPOSE 80 443 8080`

these are the ports that can be binded to the host ports via -p

the last things of the dockerfile must be the CMD stanza

`CMD ["nginx", "-g", "daemon off;"]`

this the command that will run when the container starts

## building an image

- docker image build -t beelzekamibub/cutom_nginx .

- to build an image using the dockerfile in the current directory with the name beelzekamibub/custom_nginx and since we did not provide any tag it goes to latest tag
- each layer gets a unique hash to reuse layers

when we add expose ports thats just for containers to receive packets form those ports and not for the nginx we installed in the image

when a line in the dockerfile changes all the lines after that have to built again so you should keep the lines that are changed more often after the lines that are changed less often

Before running some commands we can actually specify the workdir and copy something we need from our local machine intp the image like so

⁠ 

this path changes the default index page of the nginx webserver

if we want to send our custom image to dockerhub we have to give it the following name beelzekamibub/custom_nginx:latest with the following command

- docker image tag nginx:latest beelzekamibub/custom_nginx:latest
- docker image push beelzekamibub/custom_nginx:latest

## dockerizing a node app

linux commands that come in handy mkdir -p /usr/src/app

the -p flag creates the parent dir before creating the children dirs, so essentially it will create all the dirs in the path we give it

the clean up command that is “npm cache clean” should be used in the same line as the np install command so the RUN stanza should look like

`RUN npm install && npm cache clean`

dockerfile to build the image from the following assignment url

[https://github.com/BretFisher/udemy-docker-mastery/tree/main/dockerfile-assignment-1](https://github.com/BretFisher/udemy-docker-mastery/tree/main/dockerfile-assignment-1)

## pruning to keep our system clean

- docker image prune

- cleans up dangling images

- similarly we have

- docker container prune

- to remove all stopped containers

- docker system prune

- cleans up everything that we are not using

- docker image prune -a

- removes all images not in use

- docker system df

- to see space usage

## Persistent data and Volumes

containers are supposed to be immutable and disposable but not the data they create. persistence of data can be achieved via two ways

- volumes - persists data outside the union file system a unique location on the host
- bind mounts - binding or mounting a host directory into the container

### volumes

can be achieved via the VOLUME stanza in the dockerfile. any files we put in the volume outlive the container untill we manually delete the volume

- docker volume prune

- to clean up unused volumes

we can see the volume path via

- docker image inspect

- or

- docker container inspect

volumes path

the source is the host location where the data is stored, so the container think its writing the data to the destination but is stored in the source

- docker volume ls

- to list all volumes with its unique hash id

- docker volume inspect <volume-id>

the docker volume command doesnt tell us what container its expecting data to come from

the problem with that is that the mysql image creates a volume

⁠ 

after removing the containers there will be no way of knowing which volume was used to store which containers data, to solve this issue we have named volumes giving us the ability to specify things at the run time

using the -v flag, which gives us the ability to create new volumes at run time that is named volumes by giving a path, or assigning an existing volume to a new container at its startup

- docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True ==-v mysql-db:/var/lib/mysql== mysql

- doing this will give our volume a name which we can see with the following command

- if after removing our container mysql we run this command again with new container name that container will persist data from the previous container
- docker volume ls

⁠ 

we can create volumes before hand as well with the following command

- docker volume create --driver <driver-name> --label [label-list]

- this is required if we want to use custom driver and labels, Volume drivers let you store volumes on remote hosts or cloud providers, to encrypt the contents of volumes, or to add other functionality.
- labels are used in production

- ${pwd} - in powershell to print working directory

if you have spaces in your path, you'll usually need to quote the whole path in the docker command.

### mounting

mapping host file/dir to containers file/dir, basically having the two paths point to the same physical location on the disk, skipping the union file system, and the host files overwrite everything in the container, the container files arent actually deleted, because nothing is actually over written in the contianer, the file just wins the path reference as long as the bind mount exists, when we re run the container without it the container files persist.

bind mounts are host specific so cant be specified in the dockerfile

- docker container run -v //c/Users/chaitanya/stuff:/path/container

the way docker knows a bind mount is different from a named volume is, the bind mount starts with a ‘/‘

a bind mount doesnt require a volume to work

- docker container run -d --name nginx_mounted_index -p 80:80 -v ${pwd}:/usr/share/nginx/html nginx

- the index file at ${pwd} can now be edited as the main index file of nginx

if we delete a file from the mounted dir in the container it will be deleted on the host as well

### file permissions accross containers

we will face file permission problems when (containers not having permission to access files) we want multiple containers to access the same volumes, or when bound mounting existing files into a container, primarily we will face this issue when working with linux servers with just dockerd

file ownership in linux is represented via numbers, that is IDs which are attached to each file and dir in the file system itself, host and the container have different name to number aliases, but the IDs remain the same no matter which process accesses them

the name to numebr aliases can be found in ``/etc/passwd`` and ``/etc/group`` .

when we have multiple containers accessing the same volume or bind mounts we can have two kinds of problems

1. **Problem one: The `**`**/etc/passwd**`**` is different across containers -**

Creating a named user in one container and running as that user may use ID 700, but that same name in another container with a different ``/etc/passwd`` may use a different ID for that same username. That's why I only care about IDs when trying to sync up permissions. You'll see this confusion if you're running a container on a Linux VM and it had a volume or bind-mount. If you do an `ls` on those files from the host, it may show them owned by ubuntu or node or systemd, etc. Then if you run `ls` inside the container, it may show a different friendly username. The IDs are the same in both cases, but the host will have a different `passwd` file than the container, and show you different friendly names. **Different names are fine, because it's only ID that counts. Two processes trying to access the same file must have a matching user ID or group ID.**

3. **Problem two: Your two containers are running as different users -**

Maybe the user/group IDs and/or the USER statement in your Dockerfiles are different, and the two containers are technically running under different IDs. Different apps will end up running as different IDs. For example, the node base image creates a [user called node with ID of 1000](https://github.com/nodejs/docker-node/blob/6256caf2c507e7aafdeb8e7f837bab51f46f99e0/17/bullseye/Dockerfile#L3-L4), but the NGINX image creates an [nginx user as ID 101](https://github.com/nginxinc/docker-nginx/blob/6f0396c1e06837672698bc97865ffcea9dc841d5/mainline/debian/Dockerfile#L16-L17). Also, some apps spin-off sub-processes as different users. NGINX starts its main process (PID 1) as root (ID 0) but spawns sub-processes as the nginx user (ID 101), which keeps it more secure.

#### troubleshooting this issue

1. Use the command `ps aux` in each container to see a list of processes and usernames. The process needs a matching user ID or group ID to access the files in question.

1. update ps with

`apt-get update && apt-get install procps`

4. Find the UID/GID in each containers ``/etc/passwd`` and ``/etc/group`` to translate names to numbers. You'll likely find there a miss-match, where one containers process originally wrote the files with its UID/GID and the other containers process is running as a different UID/GID
5. Figure out a way to ensure both containers are running with either a matching user ID or group ID. This is often easier to manage in your own custom app (when using a language base image like python or node) rather than trying to change a 3rd party app's container (like nginx or postgres)... but it all depends. This may mean creating a new user in one Dockerfile and setting the startup user with USER. ([see USER docs](https://docs.docker.com/engine/reference/builder/#user)) The [node default image](https://github.com/nodejs/docker-node/blob/6256caf2c507e7aafdeb8e7f837bab51f46f99e0/17/bullseye/Dockerfile#L3-L4) has a good example of the commands for creating a user and group with hard-coded IDs:

`RUN groupadd --gid 1000 node \\` `&& useradd --uid 1000 --gid node --shell /bin/bash --create-home node``USER 1000:1000`

### other useful docker commands

⁠ 

⁠ 

connecting mongo db with mongo express example, just have them on the same network

## docker compose

used to configure relationships between containers, save the container run settings, write only once, spin them all and tear them all down with a sinlge command

comprised of two parts -

YAML - describes our solution options for containers, networks, volumes.

CLI - docker-compose tool used ofr local test/dev automation using the yaml files

docker-compose.yml has its own versions: 1,2,2.1,3,3.1 , each version defines the things you can put in the compose files

the default name for the compose file is docker-compose.yml but we can force any other name with the command docker-compose -f

compose template

compose example

[for all of the option https://docs.docker.com/compose/compose-file/03-compose-file/](https://docs.docker.com/compose/compose-file/03-compose-file/)

two service example, wordpress and mysql

the above example is same as

- docker container run - -name wordpress -p 8080:80 -e WORDPRESS_DB_PASSWORD=example -v ${pwd}/wordpress-data:/var/www/html wordpress:latest
- docker container run - -name mysql -e MYSQL_ROOT_PASSWORD=example -v ${pwd}/mysql-data:/var/lib/mysql mariadb:latest

the services in a docker compose are created on a common network

depends_on attribute

depends_on tells compose that if we are starting this container make sure to startup the dependencies as well

since 2022 docker-compose doesnt require the dash between the two words, therefore the old commands like “docker-compose up” can be now written as “docker compose up”

### compose commands

- docker compose up
- docker compose down
- docker compose down -v

- command that cleans up volumes as well after tearing down the containers

- docker image rm -f${docker image ls -q}
- docker container rm -f ${docker container ls -a -q}

docker-compose.yml file

`version:'3'``⁠` `services:` `proxy:` `image: nginx:1.11` `ports:` `- '80:80'` `volumes:` `- ./nginx.conf:/etc/nginx/conf.d/default.conf:ro` `web:` `image: httpd`

- docker container run —name proxy -p 80:80 -d -v ${pwd}/nginx.conf:/etc/nginx/conf.d/default.conf:ro nginx:1.11
- docker container run httpd

with a bound to the nginx config file, the “:ro” at the end of the mount means read only meaning i can not write to the container

- docker compose up

output

the docker compose up command also has the -d option if we dont want the logs

the containers are created on a common network

in that case if we want to see the logs we can use the command

- docker compose logs

if we go to localhost:80 now it will show the apache server webpage

- docker compose ps/ docker compose ls

- shows the containers ran by the compose

- docker compose top

- to list all the processes

- docker compose down

- to tear down the containers

somwthing we have to think about when using compose is that every app with dependencies, will also have _version requirements_ for those dependencies. If you add an app and a database to a Compose file, then that app is going to have specific database versions it is compatible with.

the service attribute has an option called build in which we can specify a dockerfile to build an image, in which we can define the complete build, so we dont have to start from images but we can start from raw source code in the docker-compose, and then we have to use the command

- docker compose build

⁠ 

we can put sql connection string in the environment variable instead of the appsettings

when we use docker compose a network is created and every service is a host, and we have to use the database host name in the connection string like so.

⁠ 

here db is the database server name and vidly is the database name

instead of list syntax for environment we can also use the object syntax

⁠ 

`service:` `build: relative-path-of-the-dockerfile`

- docker compose ls

- shows the containers started by this compose file

### drupal webserver and postgresql database example

if we do named volumes in docker compose we have to define the names in the volumes section much like the networks.

using drupal:9 postgres:14 versions

`version: "3.8"``services:` `drupal:` `image: drupal:9` `ports:` `- "8080:80"` `volumes:` `- drupal-modules:/var/www/html/modules` `- drupal-profiles:/var/www/html/profiles` `- drupal-sites:/var/www/html/sites` `- drupal-themes:/var/www/html/themes` `postgres:` `image: postgres:14` `#ports:` `#- "80:5432"` `#we dont need ports here because it will use the default one inside the network to talk to drupal` `environment:` `- POSTGRES_USER=postgres` `- POSTGRES_PASSWORD=postgres``volumes:` `drupal-modules:` `drupal-profiles:` `drupal-sites:` `drupal-themes:`

we dont need ports in the postgres section because it will use the default one inside the network to talk to drupal

server name in the drupal website has to be the name of our service because thats the name used by docker to do DNS routing

- docker container run --name drupal -d -p 8080:80 -v drupal-modules:/var/www/html/modules -v drupal-profiles:/var/www/html/profiles -v drupal-sites:/var/www/html/sites -v drupal-themes:/var/www/html/themes drupal:9
- docker container run --postgres -e POSTGRES_PASSWORD=postgres postgres:14

### image building in compse file

using build in compose file example, where the name of the docker file is not the default name

instead of using the default nginx image and mounting the volume to the config file we will be building a custome nginx and usnig the config file while building the image and not while running the image

like so

the image attribute after the build attribute is used to name the custom image, so the new image we build will be called nginx-custom

- docker compose up

- will first look for an image called nginx-custom, if not found in the local cache it will start building it

starting up this compose file will act as the hot reload for a static website

## Dockerize .Net

1. test the code using the ==dotnet run== command from the terminal
2. create dockerfile

`FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build #specifying the stage image used is for building``WORKDIR /SRC #setting the working directory inside the image and any further commnds will be relative to this` `#this also means that the files will be copied into a sub folder and not the root folder``COPY OrderProcessingWorker.csproj . #copy the proj file to the source folder``RUN dotnet restore "OrderProcessingWorker.csproj" #restore all the nuget dependencies``COPY . . #copy files from the project folder to the source folder` `RUN dotnet publish "OrderProcessingWorker.csproj" -c Release -o /publish``⁠` `FROM mcr.microsoft.com/dotnet.aspnet:5.0 AS final #image optimized for running``WORKDIR /app #this is where our finally compiled binaries will go``COPY --from=build /publish .``⁠` `EXPOSE 5000``⁠` `ENTRYPOINT ["dotnet","OrderProcessingWorker.dll"]`

1. in the termincal run the ==docker build -t beelzekamibub/MicroservicesCouponAPI:1 .== command
2. we can look for our image via the ==docker image ls | grep Microservices== command
3. to spin up the image without giving it a port number use the command ==docker run —rm -it —env-file .env beelzekamibub/MicroservicesCouponAPI:1==
4. to push up to remote repository docker push beelzekamibub/MicroservicesCouponAPI:1
5. if we have a particular port to expose the container we have the following command
6. ==docker run —rm -it -p 5000:5000 -p 5001:5001 -e ASPNETCORE_HTTP_PORT=https://+:5001 -e ASPNETCORE_URLS=http://+:5000 productive-dev/cloud-customer==
7. docker ps to see if container is still running
8. and docker container stop <container id from the prev command> to stop the container

dockerfile

make this inside the project folder

we can view the images with this command as well ==docker images==

to run this iamge as a container we can use the command ==docker run —rm -it -p 8080:80 beelzekamibub/MicroservicesCouponAPI==

### run and connect to sql server docker

we will do so with a docker command

`docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=29@vansthaliA' -e 'MSSQL_PID=Express' -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest-ubuntu`

now we can open ssms and connect by giving the server name as ==localhost,1433== in the login account give ==SA== and the ==password== we specified earlier

after this we can make the volumes of the sql server

and to start the sql server command line tools run the command

`docker exec -it <container id> /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '29@vansthaliA'`

## How to containerise Web API with Docker & use PostgreSQL

for this file to work we would have to remove the app.useHttpsRedirection fromt he program.cs of the api

`FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build``WORKDIR /app``EXPOSE 80 #for http``EXPOSE 443 #for https``⁠` `COPY *.csproj ./``RUN dotnet restore``⁠` `COPY . ./``RUN dotnet publish -c Release -o out``⁠` `FROM mcr.microsoft.com/dotnet/sdk:7.0 AS final``WORKDIR /app``COPY --from=stage /app/out .``ENTRYPOINT ["dotnet","CouponApi.csporj"]`

run the following commands

`docker build .`

the dot means build the docker file inside the current dir

we can also give it a more meaningful ID to remeber

`docker build . -t CouponApiImage``and then``docker run -p 8081:80 -e ASPNETCORE_URLS=http://+:80 couponapi`

this will give error because the data base is not setup

we can inject it as a variable through the docker compose file

## microservices docker kubernetes

1. adding Dockerfile

`FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base``WORKDIR /app``EXPOSE 80``⁠` `FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build``WORKDIR /src``COPY ["WebApiEntityFrameworkDockerSqlServer.csproj", "."]``RUN dotnet restore "./WebApiEntityFrameworkDockerSqlServer.csproj"``COPY . .``WORKDIR "/src/."``RUN dotnet build "WebApiEntityFrameworkDockerSqlServer.csproj" -c Release -o /app/build``⁠` `FROM build AS publish``RUN dotnet publish "WebApiEntityFrameworkDockerSqlServer.csproj" -c Release -o /app/publish /p:UseAppHost=false``⁠` `FROM base AS final``WORKDIR /app``COPY --from=publish /app/publish .``ENTRYPOINT ["dotnet", "WebApiEntityFrameworkDockerSqlServer.dll"]`

### Change the connection string to the database

- Connecting from one container to another on the same Docker network is done by using the container name as the hostname.
- apsettings should look like

`"ConnectionStrings": {` `"SalesDb": "Server=sql_server2022;Database=SalesDb;User Id=SA;Password=A&VeryComplex123Password;MultipleActiveResultSets=true"` `}`

- Open a terminal and navigate to the folder where the _Dockerfile_ is located.

`docker build -t web_api .`

⁠ 

`docker image ls`

- Create a file named _docker-compose.yml_ (it doesn’t matter where the file is located, but I suggest placing it in the same directory as your _Dockerfile)_ and add the following content to it:

`version: "3.9" # optional since v1.27.0``services:` `web_api:` `image: web_api` `container_name: web_api_application` `ports: #It maps port 80 inside the container to port 5000 on your computer.` `- "5000:80"` `sql:` `image: "mcr.microsoft.com/mssql/server:2022-latest"` `container_name: sql_server2022` `ports: # not actually needed, because the two services are on the same network` `- "1433:1433"` `environment:` `- ACCEPT_EULA=y` `- SA_PASSWORD=A&VeryComplex123Password`

- To start the two containers, run the following command:

`docker-compose up`

after this we wanna look at how to set up volumes for sql server

⁠ 

`docker run -p 8080:80 -d <image name> .``⁠` `docker push <image name>`

## kubernetes

docker desktop is a single cluster system of kubernetes

one pod can run multiple containers we are not going to do that

pods are not accesible to access them we have to setup node port (not really used in production), internally mapped to the port of the container

in order to allow containers to communicate with each other we have to setup something called a ClusterIP service

for production we setup and ingress nginx container, its like an API gateway, along with an nginx loadbalancer

⁠ 

at the project level we need to create another folder thats going to contain all the kubernetes assets named K8S, at the just inside solution level

1. create yaml file for each api project inside the folder

file structure

⁠ 

⁠ 

[https://www.youtube.com/watch?v=x9NptrXA1dM&ab_channel=MohamadLawand](https://www.youtube.com/watch?v=x9NptrXA1dM&ab_channel=MohamadLawand)

⁠ 

[https://youtu.be/DgVjEo3OGBI?t=11050](https://youtu.be/DgVjEo3OGBI?t=11050) iskebaad kuch smjh ni aara revise kubernetes first

⁠ 

⁠ 

⁠ 

⁠ 

⁠ 

⁠ 

⁠ 

:nest metrics:table metrics- :ul metrics
`:code-block metrics`

# :title metrics

:card metrics:divider metrics- :ol metrics
- :checklist metrics
:image metrics

### :h2 metrics

> :block-quote metrics

## :h1 metrics

:embed metrics

#### :h3 metrics

:callout metrics

:p metrics

- [Docker](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPdKvdvzgpSn3xXjz6vRWyaG)
- [docker commands](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPNLzpDS4cgCyGxodWhoawGn)
- [commands to see what goes on inside the containers](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPVBgE8MUKbns86AJP2bw8c3)
- [getting inside a container](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPkfbRpehfRCfU7Keuidg8Dg)
- [Docker Networks](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPXNiSJmyfSHg2ryjzFbgoxs)
- [cli network management](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPfUgoCnzvkAsizR1GyVSPuv)
- [DNS - how it affects containers in custom networks and default networks](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPgGBhSsz8q6VjnKfeW6HkrT)
- [dns round robin test](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPeviwoiFjiNRpeFG6ckhLod)
- [Images](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPmU3SPVLmEG4g5Bu8xvN6ZD)
- [dockerfile](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPLbZhREZzDSKjPEaCG2CYTW)
- [building an image](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPH9tVLEnbfg87hvDFffiboo)
- [dockerizing a node app](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPK4FeNBEjFwonVxLH3ndySG)
- [pruning to keep our system clean](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPkJb9S464ghAbqsV1wh2ZGf)
- [Persistent data and Volumes](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPWCx5t17xad2XGbYxxm1xSF)
- [volumes](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPGFuyyQN8jQu1J8eKCReutU)
- [mounting](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPS4HB4n9fU237WJ1JRdRC2n)
- [file permissions accross containers](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPfnDxgroGU737gDTyJXHiUL)
- [troubleshooting this issue](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPeMLSnhspr6LtMAGCAT2kpt)
- [other useful docker commands](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPd4dCLwr3kEkpSNNp6uKmJR)
- [docker compose](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPSs56CNkbe6CTHHnxRZpzcA)
- [compose commands](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPgcTodMZz2Fi7Sxiq9U9XNU)
- [drupal webserver and postgresql database example](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPXTCg8LeVBhXHED58mhQjLC)
- [image building in compse file](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPgCPT1RvmqAQcsBskJNpDNc)
- [Dockerize .Net](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPer81RJTrkFLSAToDNixzk8)
- [run and connect to sql server docker](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPLxfqx4ovL3eFdLBcYAXoLL)
- [How to containerise Web API with Docker & use PostgreSQL](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPXA1iwm2QJcNpk9PwjNAk8e)
- [microservices docker kubernetes](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPhER73LbukrDRS28PV1w2wN)
- [Change the connection string to the database](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPPp8fGNYeDs6Nn8h9NCdNXr)
- [kubernetes](https://whimsical.com/docker-VKdw42HnUDH6VzYTRkJbn4@2bsEvpTYFZsxPi29m3WM6nbunrVMVTKYkEi)