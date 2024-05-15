## Image Storage and Distribution

### Container Registries
- An image registry needs to be part of your container plan

#### Run the resgitry image
```sh
$ docker container run -d -p 5000:5000 --name registry registry
```
#### Re-tag an existing image and push it to your new registry
```sh
$ docker tag hello-world 127.0.0.1:5000/hello-world
$ docker push 127.0.0.1:5000/hello-world
```
#### Remove that image form local cache and pull it from new registry
```sh 
$ docker image remove hello-world
$ docker image remove 127.0.0.1:5000/hello-world
$ docker pull 127.0.0.1:5000/hello-world
```
#### Re-create registry using a bind mount and see how it stores data
```sh
$ docker container run -d -p 5000:5000 --name registry -v $(pwd)/registry-data:/var/lib/registry registry
```

