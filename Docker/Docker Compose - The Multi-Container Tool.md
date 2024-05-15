
Simplified control: Docker Compose **allows you to define and manage multi-container applications in a single YAML file**. This simplifies the complex task of orchestrating and coordinating various services, making it easier to manage and replicate your application environment.

Docker Compose is a tool you can use to define and share multi-container applications. This means you can run a project with multiple containers using a single source.

For example, assume you're building a project with NodeJS and MongoDB together. You can create a single image that starts both containers as a service – you don't need to start each separately.

ref: https://www.freecodecamp.org/news/what-is-docker-compose-how-to-use-it/#:~:text=Docker%20Compose%20is%20a%20tool,with%20NodeJS%20and%20MongoDB%20together.

```sh
$ docker compose --help

Usage:  docker compose [OPTIONS] COMMAND

Docker Compose

Options:
      --ansi string                Control when to print ANSI control characters ("never"|"always"|"auto") (default "auto")
      --compatibility              Run compose in backward compatibility mode
      --env-file string            Specify an alternate environment file.
  -f, --file stringArray           Compose configuration files
      --profile stringArray        Specify a profile to enable
      --project-directory string   Specify an alternate working directory
                                   (default: the path of the, first specified, Compose file)
  -p, --project-name string        Project name

Commands:
  build       Build or rebuild services
  convert     Converts the compose file to platform's canonical format
  cp          Copy files/folders between a service container and the local filesystem
  create      Creates containers for a service.
  down        Stop and remove containers, networks
  events      Receive real time events from containers.
  exec        Execute a command in a running container.
  images      List images used by the created containers
  kill        Force stop service containers.
  logs        View output from containers
  ls          List running compose projects
  pause       Pause services
  port        Print the public port for a port binding.
  ps          List containers
  pull        Pull service images
  push        Push service images
  restart     Restart containers
  rm          Removes stopped service containers
  run         Run a one-off command on a service.
  start       Start services
  stop        Stop services
  top         Display the running processes
  unpause     Unpause services
  up          Create and start containers
  version     Show the Docker Compose version information

Run 'docker compose COMMAND --help' for more information on a command.
```

###### docker-cmpose.yml is default filename, but any can be used with docker compose -f
### Docker Compose CLI
#### Two most common commands are
```sh
$ docker compose up  # setup volumes/networks and start all containers
```

```sh
$ docker compose down  # stop all containers and remove cont/vol/net
```

##### Other Commands:
```sh
$ docker compose ps
```


```sh
Usage:  docker compose [OPTIONS] COMMAND

Define and run multi-container applications with Docker.

Options:
      --ansi string                Control when to print ANSI control characters ("never"|"always"|"auto") (default "auto")
      --compatibility              Run compose in backward compatibility mode
      --dry-run                    Execute command in dry run mode
      --env-file stringArray       Specify an alternate environment file
  -f, --file stringArray           Compose configuration files
      --parallel int               Control max parallelism, -1 for unlimited (default -1)
      --profile stringArray        Specify a profile to enable
      --progress string            Set type of progress output (auto, tty, plain, quiet) (default "auto")
      --project-directory string   Specify an alternate working directory
                                   (default: the path of the, first specified, Compose file)
  -p, --project-name string        Project name

Commands:
  attach      Attach local standard input, output, and error streams to a service's running container.
  build       Build or rebuild services
  config      Parse, resolve and render compose file in canonical format
  cp          Copy files/folders between a service container and the local filesystem
  create      Creates containers for a service.
  down        Stop and remove containers, networks
  events      Receive real time events from containers.
  exec        Execute a command in a running container.
  images      List images used by the created containers
  kill        Force stop service containers.
  logs        View output from containers
  ls          List running compose projects
  pause       Pause services
  port        Print the public port for a port binding.
  ps          List containers
  pull        Pull service images
  push        Push service images
  restart     Restart service containers
  rm          Removes stopped service containers
  run         Run a one-off command on a service.
  scale       Scale services
  start       Start services
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop services
  top         Display the running processes
  unpause     Unpause services
  up          Create and start containers
  version     Show the Docker Compose version information
  wait        Block until the first service container stops
  watch       Watch build context for service and rebuild/refresh containers when files are updated

Run 'docker compose COMMAND --help' for more information on a command.
```

https://docs.docker.com/compose/reference/

#### docker compose down

```sh
$ docker compose down --help

Usage:  docker compose down

Stop and remove containers, networks

Options:
      --remove-orphans    Remove containers for services not defined in the Compose file.
      --rmi string        Remove images used by services. "local" remove only images that don't have a custom tag ("local"|"all")
  -t, --timeout int       Specify a shutdown timeout in seconds (default 10)
  -v, --volumes volumes    Remove named volumes declared in the volumes section of the Compose file and anonymous volumes attached to containers.
```