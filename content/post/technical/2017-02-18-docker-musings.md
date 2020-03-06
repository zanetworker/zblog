---
title:  "Docker Musings"
date:   2017-02-18 15:04:23
categories: [technical]
tags: [Docker, Microservices]
---


As you might know already Docker is now the defacto standard for containers. It is being used as the run-time for many clustering frameworks. Therefore, I thought about summarizing some of the important commands that you might need in your day-to-day docker life.

I hope this post will be useful for you.


### General Docker Commands
```bash
## Tagging an image
docker tage <image_id> name_of_the_image

#real time logs
docker logs --tail 1 --follow cbceaaea0ef5

# Forcing a rebuild
docker build --no-cache

# publish a port even if it was not expose in the dockerfile
docker run -p <port>

# publish all ports that were explosed in the docker file
docker run -P

# Setting up environment variables
docker run -e WEBAPP_PORT=8000 -e WEBAPP_HOST=www.example.com

# Renaming a container
docker rename <old_name> <new_name>

## using jq with Docker
docker inspect <container_name> | jq .[0] | jq keys

[
  "Architecture",
  "Author",
  "Comment",
  "Config",
  "Container",
  "ContainerConfig",
  "Created",
  "DockerVersion",
  "GraphDriver",
  "Id",
  "Os",
  "Parent",
  "RepoDigests",
  "RepoTags",
  "RootFS",
  "Size",
  "VirtualSize"
]

docker inspect <container_name> | jq .[0] | jq .DockerVersion
"1.12.3"

# using format with docker
docker inspect --format '{{ json .Created }}' <container_id>

# find the port mapping for a container e.g. port 80
docker port <containerID> 80

# manual allocation of port
docker run -d -p host_port:container_port <container>

# Finding the container IP address using format (you can use jq)
docker inspect --format '{{ .NetworkSettings.IPAddress }}' <ContainerID>

docker inspect <ContainerID> | jq .[0] | jq .NetworkSetting.IPAddress

# Removing a docker container
docker rm -f <container_name>

# Create a custm network
docker network create <network name>

##some useful options
--internal # no communication with the outside worlds
--gateway # specify the gateway of choice
--subnet # choose the subnet to use e.g., --subnet 192.168.1.0/24
--ip-range # specify which IPs from the subnet to use
--aux-address # specify reserved addresses not to be used by the network

# Create an Overlay network
docker network create mynet --driver overlay

# Create a container link
docker run -it --link datastore:redis alpine ping redis

# connecting a volume on the host to the container
docker run -d -v $(pwd):/src username/application_name

# create  a volume from anothe container (data-containers usecase)
docker run --volumes-from <source_container> <destination_container> <commands>
# list available volume
docker volumes ls

# craete named volumes
docker volume create --name <volume_name>

```

## Writing a Docker File
Exampole of a Docker File:

```bash
# Fetch an image called node
FROM node
# Maintainer
MAINTAINER maintainer@email.com
# Run commands inside the container (typicall used for installing packages)
# Each RUN command adds a new Docker layer, so make sure to use them wisely (use RUN Command1 && Command2 && .. CommandN to save layers and storage)
RUN git clone -q https://github.com/docker-practice/test.git && npm install > /dev/null
# Change to a directory called test (this is where CMD commands will be executed )
WORKDIR ~/test
# Expose port 8000 inside the container (that will be mapped by default to another host port)
EXPOSE 8000
# CMD defines the command to be executed when the container is started
CMD ["start"]
# ENTRY point sets the default container command
ENTRYPOINT ["npm"]

#Note_1: Use ENTRY Point when you need to run your container as an executable and use CMD to provide arguments to ENTRYPOINT commands.

#Note_2: you can only use one CMD and one ENTRYPOINT per Dockerfile
```

## What are the components of a compose file?

The core components are:

- **version**: mandatory
- **services**: mandatory
- **networks**: optional (defines the networks that container can utilize)
- **volumes**: optional (defines the volumes to be used and shared by containers)

Example compose file:

```bash
version: '2'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

## How to install Docker-compose?

Download it and add it to the bin path:

```bash
sudo curl -L https://github.com/docker/compose/releases/download/1.11.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

Then enable execute permissions:

```bash
chmod +x /usr/local/bin/docker-compose

```

Finally, check that it is correctly installed by typing ```docker-coompose --version```

## How to use Docker compose?

```bash
##Build your compose setup
docker-compose build

## Run it
docker-compose up

## Run it in detached mode
docker-compose up -d

## Check the status of the compose containers
docker-compose ps

## kill / remove your compose setup
docker-compose kill
docker-compose rm
```

## How to create container link?

```bash
docker run -it --link datastore:redis alpine ping redis
```

## What is a network alias
Imagine the case where you have more than one container doing the webserver duty and you want to give them the same name on the network (e.g., for loadbalancing). The solution your looking for might be network alias.

```bash
# Creating Network Alias
docker run -d --name prod-es-1 --net-alias search --net prod elasticsearch
docker run -d --name prod-es-2 --net-alias search --net prod elasticsearch

```

- Docker network alias only work with custom networks
- Docker automates the creation of network alias
- Starting Docker 1.12, swarm mode contains advanced features for load-balancing

## How to enable multi-host communication communication?

Use overlay networking. To configure overlay networking, do the following:

```bash
docker network create mynet --driver overlay
```

## How to upload images to Dockerhub

Here are the steps required for uploading Images to Dockerhub

- Create a dockerhub account
- tag the image with "username/imagename"
  - ```docker tag <image_id> username/imagename```
- Push your image to docker hub
  - ```docker push username/imagename```
