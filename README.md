## Docker

Docker images package an application environment and uses the Docker engine, a runtime layer on the host operating system, to access resources from the underlying operating system.

### Commands

`docker search <search-term>`
: searches DockerHub for an image

`docker container ls`
: list running containers.
  `-a` flag lists running and stopped containers

`docker run <image-name>`
: Create a container from an image. Returns a prompt formatted as follows: `root@<container-id>:/#`
  `-i` flag to run it interactively
  `-t` flag creates a terminal

`docker commit <container-id> image name`
: Build an image from a container. Generally, you just ran the container and installed dependencies that you want to save as an image.

`docker build -t <image-name> <dockerfile-directory>`
: Builds an image from a Dockerfile.
  `-t` flag names the image, with an optional tag. The tag defaults to `latest`.


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

A container is a running instance of an image. Containers have state: you can interact with containers, and create multiple, unique instances of the same container.

### Building images

Build images with the `docker commit` command, or automate builds with a Dockerfile.

With docker commit, you run a base image and install dependencies. Then, you exit the container, and run docker commit <digest> <image-name> to build a custom image. The following steps build an ubuntu image with git:

1. $ docker run -it ubuntu:18.04 /bin/bash
2. # apt-get update && apt-get install git -y
3. # which git
4. # exit
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
`ENTRYPOINT`: Specifies which application the container executes.