
#### Containers Lifetime & Persistent Data
	1. Containers are usually immutable and ephemeral
	2. "Immutable infrastructure": only re-deploy containers, never change
	3. This is the ideal scenario, but what about databases, or unique data?
	4. Docker gives us a feature to ensure these "separation of concerns"
	5. This is known as "persistent data"
	6. Two ways: Volumes and Bind Mounts
	7. Volumes: make special location outside of container UFS
	8. Bind Mounts: link contaienr path to host path



Containers are supposed to be immutable and disposable but not the data they create. persistence of data can be achieved via two ways
	1. Volumes - persists data outside the union file system a unique location on the host
	2. Bind mounts - binding or mounting a host directory into the container
###### no matter how often you delete and recreate the containers, Docker Desktop persists your data and it's accessible to any container on your system by mounting the `database` volume. Docker Desktop looks for the `database` volume and creates it if it doesn't exist.

```sh
$ docker volume --help

Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove unused local volumes
  rm          Remove one or more volumes

Run 'docker volume COMMAND --help' for more information on a command.
```

```sh
$ docker volume ls
```

```sh
$ docker volume inspect <volume-name>
```

###### After removing the containers there will be no way of knowing which volume was used to store which containers data, to solve this issue we have ***named volumes*** giving us the ability to specify things at the run time
	Using the -v flag, which gives us the ability to create new volumes at run time that is named volumes by giving a path, or assigning an existing volume to a new container at its startup

```sh
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
```
	doing this will give our volume a name which we can see with the following command

###### if after removing our container mysql we run this command again with new container name that container will persist data from the previous container
![[Pasted image 20240226170919.png]]

### Docker Volume Create
Required to do this before "docker run" to use custom drivers and labels
	
```sh
$ docker volume create --help

Usage:  docker volume create [OPTIONS] [VOLUME]

Create a volume

Options:
  -d, --driver string   Specify volume driver name (default "local")
      --label list      Set metadata for a volume
  -o, --opt map         Set driver specific options (default map[])
```


#### Persistent Data: Bind Mounting
	1. Maps a host file or directory to a container file or directory
	2. Basically just two locations pointing to the same file(s)
	3. Again, skips UFS, and host files overwrite any in container


### Mounting
Mapping host file/dir to containers file/dir, basically having the two paths point to the same physical location on the disk, skipping the union file system, and the host files overwrite everything in the container, the container files arent actually deleted, because nothing is actually over written in the contianer, the file just wins the path reference as long as the bind mount exists, when we re run the container without it the container files persist.

>Bind mounts are host specific so cant be specified in the dockerfile

```sh
$ docker container run -v //c/Users/chaitanya/stuff:/path/container
```

> The way docker knows a bind mount is different from a named volume is, the bind mount starts with a ‘/‘


>A bind mount doesnt require a volume to work

```sh
$ docker container run -d --name nginx_mounted_index -p 80:80 -v ${pwd}:/usr/share/nginx/html nginx
```

>the index file at ${pwd} can now be edited as the main index file of nginx

##### If we delete a file from the mounted dir in the container it will be deleted on the host as well



