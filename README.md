## Docker

Docker images package an application environment and uses the Docker engine, a runtime layer on the host operating system, to access resources from the underlying operating system.

### Commands

`docker search <search-term>`
: searches DockerHub for an image

`docker container ls | docker ps`
: list running containers.
  `-a` lists running and stopped containers

`docker run <image-name>`
: Create a container from an image. Returns a prompt formatted as follows: `root@<container-id>:/#`
  `-i` to run it interactively
  `-t` creates a terminal
  `-d` runs the container as a daemon
  `-p` publishes a port and provides a mapping in <host-port>:<container-port> format
  `-P` automatically publishes all exposed ports to unused host ports
  `-v` mount a volume using absolute paths in the `<host-path>:<container-path>` format
  `-name` assign the container a name
  `-rm` remove the container from the Docker host as soon as it stops

`docker commit <container-id> image name`
: Build an image from a container. Generally, you just ran the container and installed dependencies that you want to save as an image.

`docker build -t <image-name> <dockerfile-directory>`
: Builds an image from a Dockerfile
  `-t` assigns a tag to the image. The tag defaults to `latest`. Tags use the `<registry-address>/<image-name>:<version>` format
  `-e` defines environment variables. Replace this with an `ENV` in the Dockerfile.

`docker logs <container-id>`
: Displays logs in the container

`docker port <container-id>`
: Displays container and host port mappings

`docker inspect <container-id>`
: Displays all of the configuration information about the container

`docker rm <container-id>`
: Delete the container from the Docker host. You must stop the container first.

`docker rm $(docker ps --no-trunc -aq)`
: Remove all stopped containers from the Docker host. `--no-trunc` prints the full data to the console, and `-aq` passes only the IDs for the containers.

`docker images`
: List all images on the Docker host

`docker rmi <image-id>`
: Delete the image

`docker rmi $(docker images -q)`
: Removes all old and unused images

`docker rmi $(docker images -f "dangling=true" -q)`
: Removes all images that do not have tags (such as :latest)


### Components

Docker daemon
: The daemon runs on the host all the time as a service.

REST API
: 

Docker client
: The CLI tool that connects to either the daemon or the REST API

### Images and containers

The Docker image is a recipe for running your application. It contains the files, executables, and dependencies--everything that you need to run your application--and stores it in a stateless artifact that you can send over the network, store, version, and save.

Docker images consist of layers of individual images that build the final runtime environment. Each image begins with a base layer, which is the operating system. Add dependencies, such as a JDK image, as a layer, until you construct the final environment.

When you install an image, the Docker daemon checks whether the image is currently installed on your system. If it is, it reuses the layer to preserve bandwidth and storage.

A container is a running instance of an image. Containers have state: you can interact with containers, and create multiple, unique instances of the same container. After you stop a container, it is in the exited state and remains available on the Docker host. Containers have the following states:
- Running (`docker run`)
- Paused (`docker pause`)
- Stopped/Exited (`docker stop | kill`)
- Restarting (`docker start | create`)

To view docker containers, use the `docker container ls` or `docker ps` commands.

### Building images

Build images with the `docker commit` command, or automate builds with a Dockerfile.

With docker commit, you run a base image and install dependencies. Then, you exit the container, and run docker commit <digest> <image-name> to build a custom image. The following steps build an ubuntu image with git:

1. $ docker run -it ubuntu:18.04 /bin/bash
2. \# apt-get update && apt-get install git -y
3. \# which git
4. \# exit
5. $ docker commit <container-id> image name

A Dockerfile consists of a series of instructions that build an image. Dockerfiles replace the `docker commit` command.

After you create the Dockerfile, use `docker build` to create an image from the Dockerfile:

``` bash
$ docker build -t <image-name> <dockerfile-directory>
```

Common Dockerfile instructions include:

`FROM`: The image you are using to build the new image (the base image).
`RUN`: Commands that you want to run inside the container.
`COPY`: Copies a file or directory from the host machine into the container.
`ENV`: Define env vars in the `ENV <var-name> <value>` format.
`EXPOSE`: Stipulates that the port number provided should be published (exposed) from the container.
`VOLUME /container-path`: persists data in the `/container-path` to the `/var/lib/docker/vfs/` path on the host machine.
`ENTRYPOINT`: Specifies which application the container executes.

### Networking

To access or expose a container process to the network, you must map the port exposed within the container to a port on the host machine with the `docker run -p <host-port>:<container-port> <container>` command. You can also publish to the host network interface with the `docker run -p <ip><host-port:<container-port> <container>` mapping.

The Docker daemon creates a network interface to connect with the Docker container, for example on 172.17.0.1. If you run `docker inspect` on a container, you see that it has an IP address of 172.17.0.2 and a host with an IP address of 172.17.0.1 (the `Gateway` value). This means that you can access the port within the container at 172.17.0.2:<port>. However, this is useful only if you are running on a local machine. You almost always want to publish the port number to make it publicly available.

To assign ports automatically, use the `-P` flag without specifying a port. This publishes all exposed ports to unused host ports.

You can configure the container network with the `docker run --network <option>` command using one of the following options:
- `bridge`: Default. You specify host and container port mappings.
- `none`: The container is not exposed to the network.
- `container`: Join a network with another container
- `host`: Use the same network interfaces as the host.

### Docker volumes

Docker volumes persist container-generated data to a location outside the container. A volume is a host directory mounted inside the container so that the container can write to the host filesystem.

Use `docker run -v <host-path>:<container-path>` to mount a host filepath in the container (paths must be absolute). Additionally, you can use the `VOLUME` command in a Dockerfile. When you combine the Dockerfile command with the `-v` flag, the `-v` flag takes precedence.

If you stop a running container with a volume mount, and then run a different container with the same volume mount, then the data from the host machine is mounted in the newly running container.

### Naming Docker containers

When you run a container, Docker generates a name and assigns it to the container. Docker containers with custom names are easier to work with, and they might help with environments that use naming conventions for automation. Assign a name to a container with the `--name` flag:

`$ docker run -d --name <name> <image-name>`

For many `docker ...` commands, you can use the container's name in place of the container ID. For example, `docker logs <container-name>`.